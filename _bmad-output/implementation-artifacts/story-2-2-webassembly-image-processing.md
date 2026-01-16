---
story_id: "2-2"
story_title: "WebAssembly Image Processing"
epic: "Epic 2: Core Image Processing Engine"
status: "ready-for-dev"
priority: "high"
assigned_to: ""
estimated_hours: 8
created_date: "2026-01-16"
updated_date: "2026-01-16"
tags: ["webassembly", "image-processing", "performance", "rust"]
---

# Story 2.2: WebAssembly Image Processing

As a user,
I want to experience instant client-side image processing through WebAssembly,
So that I can see real-time transformations without server delays.

## Acceptance Criteria

**Given** I have uploaded images and see thumbnail previews
**When** WebAssembly processing engine analyzes each image
**Then** Basic resizing and cropping operations complete within 300ms per image
**And** Processing results are displayed instantly without page reloads
**And** Multiple images are processed in parallel without blocking the UI
**And** Processing progress is shown with confidence indicators
**And** Failed processing displays clear error messages without crashing the interface
**And** Processed images maintain original quality and aspect ratio options
**And** Batch processing completes within 2 seconds for up to 50 images
**And** WebAssembly memory usage stays within browser limits

## Implementation Notes

### Prerequisites
- Completed Story 2.1: Image Upload & Preview
- Rust toolchain and wasm-pack installed
- WebAssembly build pipeline configured
- Performance monitoring system active

### Technical Requirements
- Implement Rust image processing functions compiled to WebAssembly
- Create TypeScript bindings for WASM functions
- Build parallel processing queue for multiple images
- Integrate with performance monitoring system
- Handle memory management and cleanup
- Provide progress feedback during processing

### Rust WebAssembly Functions

#### Core Processing Functions
```rust
use wasm_bindgen::prelude::*;
use image::{ImageBuffer, DynamicImage};

#[wasm_bindgen]
pub struct ProcessedImage {
    pub data: Vec<u8>,
    pub width: u32,
    pub height: u32,
    pub format: String,
}

#[wasm_bindgen]
impl ProcessedImage {
    #[wasm_bindgen(constructor)]
    pub fn new() -> ProcessedImage {
        ProcessedImage {
            data: Vec::new(),
            width: 0,
            height: 0,
            format: "jpeg".to_string(),
        }
    }
}

#[wasm_bindgen]
pub fn resize_image(image_data: &[u8], width: u32, height: u32) -> ProcessedImage {
    // Load image from bytes
    let img = image::load_from_memory(image_data)
        .unwrap_or_else(|_| panic!("Failed to load image"));
    
    // Resize with high quality filter
    let resized = img.resize(width, height, image::imageops::FilterType::Lanczos3);
    
    // Convert back to bytes
    let mut buffer = Vec::new();
    resized.write_to(&mut std::io::Cursor::new(&mut buffer), image::ImageFormat::Jpeg)
        .unwrap_or_else(|_| panic!("Failed to encode image"));
    
    ProcessedImage {
        data: buffer,
        width: resized.width(),
        height: resized.height(),
        format: "jpeg".to_string(),
    }
}

#[wasm_bindgen]
pub fn crop_image(image_data: &[u8], x: u32, y: u32, width: u32, height: u32) -> ProcessedImage {
    let img = image::load_from_memory(image_data)
        .unwrap_or_else(|_| panic!("Failed to load image"));
    
    // Crop the image
    let cropped = img.crop(x, y, width, height);
    
    let mut buffer = Vec::new();
    cropped.write_to(&mut std::io::Cursor::new(&mut buffer), image::ImageFormat::Jpeg)
        .unwrap_or_else(|_| panic!("Failed to encode image"));
    
    ProcessedImage {
        data: buffer,
        width: cropped.width(),
        height: cropped.height(),
        format: "jpeg".to_string(),
    }
}

#[wasm_bindgen]
pub fn get_image_info(image_data: &[u8]) -> JsValue {
    let img = image::load_from_memory(image_data)
        .unwrap_or_else(|_| panic!("Failed to load image"));
    
    let info = js_sys::Object::new();
    js_sys::Reflect::set(&info, &"width".into(), &img.width().into());
    js_sys::Reflect::set(&info, &"height".into(), &img.height().into());
    js_sys::Reflect::set(&info, &"color_type".into(), &format!("{:?}", img.color()).into());
    
    info.into()
}
```

### TypeScript Integration Layer

#### WebAssembly Processing Hook
```typescript
interface ProcessingOptions {
  resize?: { width: number; height: number }
  crop?: { x: number; y: number; width: number; height: number }
  quality?: number
  format?: 'jpeg' | 'png' | 'webp'
}

interface ProcessingResult {
  data: Uint8Array
  width: number
  height: number
  format: string
  processingTime: number
}

export const useWebAssemblyProcessing = () => {
  const [isProcessing, setIsProcessing] = useState(false)
  const [progress, setProgress] = useState(0)
  const { trackWebAssemblyProcessing } = usePerformanceContext()

  const processImage = useCallback(async (
    imageData: ArrayBuffer,
    options: ProcessingOptions = {}
  ): Promise<ProcessingResult> => {
    const startTime = performance.now()
    setIsProcessing(true)
    setProgress(0)

    try {
      // Load WebAssembly module
      const wasm = await import('../wasm/pkg/pick_sizer_wasm')
      
      // Convert ArrayBuffer to Uint8Array
      const uint8Array = new Uint8Array(imageData)
      
      let result
      if (options.resize) {
        setProgress(25)
        result = wasm.resize_image(
          uint8Array,
          options.resize.width,
          options.resize.height
        )
      } else if (options.crop) {
        setProgress(50)
        result = wasm.crop_image(
          uint8Array,
          options.crop.x,
          options.crop.y,
          options.crop.width,
          options.crop.height
        )
      } else {
        throw new Error('No processing options provided')
      }
      
      setProgress(75)
      
      const processingTime = performance.now() - startTime
      trackWebAssemblyProcessing(processingTime)
      
      setProgress(100)
      
      return {
        data: result.data,
        width: result.width,
        height: result.height,
        format: result.format,
        processingTime
      }
    } catch (error) {
      console.error('WebAssembly processing failed:', error)
      throw error
    } finally {
      setIsProcessing(false)
      setProgress(0)
    }
  }, [trackWebAssemblyProcessing])

  return { processImage, isProcessing, progress }
}
```

### Batch Processing Queue
```typescript
interface ProcessingJob {
  id: string
  imageData: ArrayBuffer
  options: ProcessingOptions
  onProgress: (progress: number) => void
  onComplete: (result: ProcessingResult) => void
  onError: (error: Error) => void
}

export class ProcessingQueue {
  private queue: ProcessingJob[] = []
  private processing = false
  private maxConcurrency = 4

  add(job: ProcessingJob) {
    this.queue.push(job)
    this.processNext()
  }

  private async processNext() {
    if (this.processing || this.queue.length === 0) return
    
    this.processing = true
    const jobs = this.queue.splice(0, Math.min(this.maxConcurrency, this.queue.length))
    
    try {
      await Promise.all(
        jobs.map(job => this.processJob(job))
      )
    } finally {
      this.processing = false
      if (this.queue.length > 0) {
        this.processNext()
      }
    }
  }

  private async processJob(job: ProcessingJob) {
    try {
      job.onProgress(0)
      const result = await this.processImageData(job.imageData, job.options)
      job.onProgress(100)
      job.onComplete(result)
    } catch (error) {
      job.onError(error as Error)
    }
  }
}
```

### File Structure Changes
```
pick-sizer/
├── wasm/
│   ├── src/
│   │   ├── lib.rs (modified)
│   │   ├── processing.rs (new)
│   │   └── utils.rs (modified)
│   └── Cargo.toml (modified)
├── src/
│   ├── components/
│   │   ├── processing/
│   │   │   ├── processing-progress.tsx (new)
│   │   │   ├── batch-processor.tsx (new)
│   │   │   └── processing-result.tsx (new)
│   │   └── wasm/
│   │       └── wasm-processor.tsx (new)
│   ├── hooks/
│   │   ├── use-webassembly-processing.ts (new)
│   │   └── use-processing-queue.ts (new)
│   └── types/
│       └── processing.ts (new)
```

### Performance Requirements
- Single image processing < 300ms
- Batch processing (50 images) < 2 seconds
- Memory usage < 200MB peak
- UI thread blocking < 50ms
- WebAssembly initialization < 100ms

### Error Handling
- WebAssembly module loading failures
- Image format incompatibility
- Memory allocation failures
- Processing timeout handling
- Graceful degradation to JavaScript fallback

### Integration Points
- Performance monitoring system
- Image upload component
- Progress indicators
- Error boundaries
- Skeleton UI components

## Definition of Done

- [ ] Rust image processing functions implemented
- [ ] WebAssembly build pipeline updated
- [ ] TypeScript bindings generated
- [ ] Processing queue system implemented
- [ ] Progress tracking functional
- [ ] Error handling complete
- [ ] Performance requirements met
- [ ] Memory management optimized
- [ ] Integration with upload component
- [ ] Unit tests for Rust functions
- [ ] Integration tests for processing pipeline
- [ ] Performance benchmarks passing
- [ ] Documentation updated

## Risks & Mitigations

**Risk**: WebAssembly memory leaks with large batches
**Mitigation**: Implement proper memory cleanup and batch size limits

**Risk**: Browser compatibility issues with WebAssembly
**Mitigation**: Feature detection and JavaScript fallback

**Risk**: Performance degradation with complex operations
**Mitigation**: Implement progressive processing and user feedback

## Related Stories

- **Story 2.1**: Image Upload & Preview (prerequisite)
- **Story 2.3**: Semantic Object Detection (next step)
- **Story 1.3**: WebAssembly Build Pipeline Setup (dependency)

---
story_id: "2-3"
story_title: "Semantic Object Detection"
epic: "Epic 2: Core Image Processing Engine"
status: "partially-completed"
priority: "high"
assigned_to: ""
estimated_hours: 10
created_date: "2026-01-16"
updated_date: "2026-01-16"
tags: ["ai", "object-detection", "machine-learning", "computer-vision"]
---

# Story 2.3: Semantic Object Detection

As a user,
I want to see AI-detected objects (faces, text, logos) highlighted on my images,
So that I can understand what the system recognizes and trust crop safety decisions.

## Acceptance Criteria

**Given** I have uploaded images and WebAssembly processing is complete
**When** semantic object detection engine analyzes each image
**Then** Faces are detected and highlighted with bounding boxes
**And** Text regions are identified and marked as protected areas
**And** Logos and brand elements are detected and outlined
**And** Detection results are displayed within 500ms of processing completion
**And** Confidence scores are shown for each detected object
**And** False positives can be corrected through user feedback
**And** Detection accuracy improves over time through learning
**And** Multiple object types can be detected in the same image
**And** Detection results are stored for future processing

## Tasks/Subtasks

- [x] Create detection types interface (DetectedObject, BoundingBox, DetectionResult, ObjectDetector)
- [x] Implement face detection engine with ML model integration
- [x] Implement text detection engine using edge detection
- [x] Implement logo detection engine using template matching
- [x] Create detection overlay component with bounding boxes
- [x] Implement confidence scoring and visualization
- [x] Create user feedback mechanism for false positives
- [x] Implement learning system integration
- [x] Create use-object-detection hook
- [x] Create use-detection-feedback hook
- [x] Write unit tests for all detectors
- [x] Write integration tests for detection pipeline
- [x] Write UI tests for overlay component

## Implementation Notes

### Prerequisites
- Completed Story 2.2: WebAssembly Image Processing
- Image processing pipeline functional
- Performance monitoring system active
- Basic UI components available

### Technical Requirements
- Implement face detection using computer vision algorithms
- Create text region detection using OCR or edge detection
- Build logo detection using template matching or ML models
- Design bounding box overlay system
- Implement confidence scoring and visualization
- Create feedback mechanism for false positive correction
- Store detection results for learning system

### Object Detection Architecture

#### Detection Engine Interface
```typescript
interface DetectedObject {
  id: string
  type: 'face' | 'text' | 'logo'
  boundingBox: BoundingBox
  confidence: number
  metadata?: Record<string, any>
}

interface BoundingBox {
  x: number
  y: number
  width: number
  height: number
}

interface DetectionResult {
  objects: DetectedObject[]
  processingTime: number
  imageMetadata: {
    width: number
    height: number
    format: string
  }
}

interface ObjectDetector {
  detect(imageData: ImageData): Promise<DetectionResult>
  train(feedback: DetectionFeedback): Promise<void>
  getModelInfo(): ModelInfo
}
```

#### Face Detection Implementation
```typescript
export class FaceDetector implements ObjectDetector {
  private model: any // TensorFlow.js or similar ML model
  
  async detect(imageData: ImageData): Promise<DetectionResult> {
    const startTime = performance.now()
    
    try {
      // Preprocess image for face detection
      const processedImage = this.preprocessImage(imageData)
      
      // Run face detection model
      const faces = await this.model.estimateFaces(processedImage, false)
      
      // Convert to standardized format
      const detectedObjects: DetectedObject[] = faces.map((face, index) => ({
        id: `face-${index}`,
        type: 'face',
        boundingBox: {
          x: face.boundingBox.topLeft[0],
          y: face.boundingBox.topLeft[1],
          width: face.boundingBox.bottomRight[0] - face.boundingBox.topLeft[0],
          height: face.boundingBox.bottomRight[1] - face.boundingBox.topLeft[1]
        },
        confidence: face.probability,
        metadata: {
          landmarks: face.landmarks,
          gender: face.gender,
          age: face.age
        }
      }))
      
      const processingTime = performance.now() - startTime
      
      return {
        objects: detectedObjects,
        processingTime,
        imageMetadata: {
          width: imageData.width,
          height: imageData.height,
          format: 'rgba'
        }
      }
    } catch (error) {
      console.error('Face detection failed:', error)
      throw error
    }
  }
  
  private preprocessImage(imageData: ImageData): ImageData {
    // Convert to RGB, normalize, resize for model input
    const canvas = document.createElement('canvas')
    const ctx = canvas.getContext('2d')!
    
    // Standardize input size for the model
    canvas.width = 224
    canvas.height = 224
    
    ctx.drawImage(imageData as any, 0, 0, 224, 224)
    return ctx.getImageData(0, 0, 224, 224)
  }
}
```

#### Text Detection Implementation
```typescript
export class TextDetector implements ObjectDetector {
  async detect(imageData: ImageData): Promise<DetectionResult> {
    const startTime = performance.now()
    
    try {
      // Use edge detection + contour analysis for text regions
      const textRegions = await this.detectTextRegions(imageData)
      
      const detectedObjects: DetectedObject[] = textRegions.map((region, index) => ({
        id: `text-${index}`,
        type: 'text',
        boundingBox: region.boundingBox,
        confidence: region.confidence,
        metadata: {
          textContent: region.text, // If OCR is available
          language: region.language,
          fontSize: region.fontSize
        }
      }))
      
      const processingTime = performance.now() - startTime
      
      return {
        objects: detectedObjects,
        processingTime,
        imageMetadata: {
          width: imageData.width,
          height: imageData.height,
          format: 'rgba'
        }
      }
    } catch (error) {
      console.error('Text detection failed:', error)
      throw error
    }
  }
  
  private async detectTextRegions(imageData: ImageData): Promise<TextRegion[]> {
    // Implement edge detection using Canny or similar
    // Find contours that resemble text
    // Filter by aspect ratio and size
    // Optional: Use Tesseract.js for OCR
    return []
  }
}
```

#### Logo Detection Implementation
```typescript
export class LogoDetector implements ObjectDetector {
  private logoTemplates: Map<string, ImageData> = new Map()
  
  async detect(imageData: ImageData): Promise<DetectionResult> {
    const startTime = performance.now()
    
    try {
      // Multi-scale template matching
      const logoMatches = await this.detectLogos(imageData)
      
      const detectedObjects: DetectedObject[] = logoMatches.map((match, index) => ({
        id: `logo-${index}`,
        type: 'logo',
        boundingBox: match.boundingBox,
        confidence: match.confidence,
        metadata: {
          brandName: match.brandName,
          templateId: match.templateId,
          matchScore: match.matchScore
        }
      }))
      
      const processingTime = performance.now() - startTime
      
      return {
        objects: detectedObjects,
        processingTime,
        imageMetadata: {
          width: imageData.width,
          height: imageData.height,
          format: 'rgba'
        }
      }
    } catch (error) {
      console.error('Logo detection failed:', error)
      throw error
    }
  }
  
  private async detectLogos(imageData: ImageData): Promise<LogoMatch[]> {
    // Implement template matching at multiple scales
    // Use edge detection for robustness
    // Apply confidence thresholding
    return []
  }
}
```

### Detection Overlay Component
```typescript
interface DetectionOverlayProps {
  imageUrl: string
  detections: DetectedObject[]
  onObjectClick?: (object: DetectedObject) => void
  onFeedback?: (objectId: string, isCorrect: boolean) => void
  showConfidence?: boolean
}

export const DetectionOverlay: React.FC<DetectionOverlayProps> = ({
  imageUrl,
  detections,
  onObjectClick,
  onFeedback,
  showConfidence = true
}) => {
  const [selectedObject, setSelectedObject] = useState<string | null>(null)
  
  const getObjectColor = (type: string) => {
    switch (type) {
      case 'face': return '#10b981' // Green
      case 'text': return '#f59e0b' // Amber
      case 'logo': return '#ef4444' // Red
      default: return '#6b7280' // Gray
    }
  }
  
  return (
    <div className="relative inline-block">
      <img 
        src={imageUrl} 
        alt="Detection overlay"
        className="max-w-full h-auto"
      />
      
      <svg className="absolute inset-0 w-full h-full">
        {detections.map((obj) => (
          <g key={obj.id}>
            <rect
              x={obj.boundingBox.x}
              y={obj.boundingBox.y}
              width={obj.boundingBox.width}
              height={obj.boundingBox.height}
              fill="none"
              stroke={getObjectColor(obj.type)}
              strokeWidth="2"
              className="cursor-pointer hover:opacity-80"
              onClick={() => {
                setSelectedObject(obj.id)
                onObjectClick?.(obj)
              }}
            />
            
            {showConfidence && (
              <text
                x={obj.boundingBox.x}
                y={obj.boundingBox.y - 5}
                fill={getObjectColor(obj.type)}
                fontSize="12"
                className="select-none"
              >
                {Math.round(obj.confidence * 100)}%
              </text>
            )}
            
            {selectedObject === obj.id && onFeedback && (
              <g>
                <rect
                  x={obj.boundingBox.x}
                  y={obj.boundingBox.y + obj.boundingBox.height + 5}
                  width={80}
                  height={30}
                  fill="white"
                  stroke="black"
                  strokeWidth="1"
                  rx="4"
                />
                <text
                  x={obj.boundingBox.x + 10}
                  y={obj.boundingBox.y + obj.boundingBox.height + 25}
                  fill="black"
                  fontSize="12"
                  className="cursor-pointer"
                  onClick={() => onFeedback(obj.id, true)}
                >
                  ✓ Correct
                </text>
                <text
                  x={obj.boundingBox.x + 45}
                  y={obj.boundingBox.y + obj.boundingBox.height + 25}
                  fill="black"
                  fontSize="12"
                  className="cursor-pointer"
                  onClick={() => onFeedback(obj.id, false)}
                >
                  ✗ Wrong
                </text>
              </g>
            )}
          </g>
        ))}
      </svg>
    </div>
  )
}
```

### File Structure Changes
```
pick-sizer/
├── src/
│   ├── components/
│   │   ├── detection/
│   │   │   ├── detection-overlay.tsx (new)
│   │   │   ├── object-list.tsx (new)
│   │   │   └── confidence-badge.tsx (new)
│   │   └── ai/
│   │       ├── face-detector.ts (new)
│   │       ├── text-detector.ts (new)
│   │       ├── logo-detector.ts (new)
│   │       └── detection-engine.ts (new)
│   ├── hooks/
│   │   ├── use-object-detection.ts (new)
│   │   └── use-detection-feedback.ts (new)
│   ├── types/
│   │   └── detection.ts (new)
│   └── utils/
│       ├── image-processing.ts (modified)
│       └── detection-utils.ts (new)
```

### Performance Requirements
- Face detection < 200ms per image
- Text detection < 300ms per image
- Logo detection < 400ms per image
- Total detection processing < 500ms
- Memory usage < 150MB
- UI updates maintain 60fps

### Learning System Integration
```typescript
interface DetectionFeedback {
  objectId: string
  imageId: string
  originalDetection: DetectedObject
  userCorrection: 'correct' | 'false-positive' | 'missed-object'
  correctedObject?: DetectedObject
  timestamp: number
}

export class DetectionLearningSystem {
  private feedbackHistory: DetectionFeedback[] = []
  
  async recordFeedback(feedback: DetectionFeedback): Promise<void> {
    this.feedbackHistory.push(feedback)
    
    // Update model parameters based on feedback
    await this.updateModels(feedback)
    
    // Store for future training
    await this.persistFeedback(feedback)
  }
  
  private async updateModels(feedback: DetectionFeedback): Promise<void> {
    // Adjust detection thresholds
    // Update confidence scores
    // Retrain with new examples
  }
  
  getDetectionAccuracy(): number {
    const total = this.feedbackHistory.length
    const correct = this.feedbackHistory.filter(f => f.userCorrection === 'correct').length
    return total > 0 ? correct / total : 0
  }
}
```

## Definition of Done

- [ ] Face detection implemented and functional
- [ ] Text region detection implemented
- [ ] Logo detection implemented
- [ ] Detection overlay component created
- [ ] Confidence scoring and visualization
- [ ] User feedback mechanism implemented
- [ ] Learning system integration
- [ ] Performance requirements met
- [ ] Error handling complete
- [ ] Unit tests for all detectors
- [ ] Integration tests for detection pipeline
- [ ] UI tests for overlay component
- [ ] Documentation updated

## Risks & Mitigations

**Risk**: False positives in object detection
**Mitigation**: Implement confidence thresholds and user feedback system

**Risk**: Performance impact with complex models
**Mitigation**: Use WebAssembly for CPU-intensive operations

**Risk**: Privacy concerns with face detection
**Mitigation**: Process client-side only, clear data after processing

## Related Stories

- **Story 2.2**: WebAssembly Image Processing (prerequisite)
- **Story 2.4**: Image Classification (next step)
- **Story 4.3**: Safe Zone Overlays (will use detection results)

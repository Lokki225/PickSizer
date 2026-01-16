---
story_id: "2-5"
story_title: "Basic Cropping Interface"
epic: "Epic 2: Core Image Processing Engine"
status: "ready-for-dev"
priority: "high"
assigned_to: ""
estimated_hours: 10
created_date: "2026-01-16"
updated_date: "2026-01-16"
tags: ["ui", "cropping", "interaction", "user-interface"]
---

# Story 2.5: Basic Cropping Interface

As a user,
I want to manually adjust crop areas with visual feedback and real-time preview,
So that I can fine-tune AI suggestions while maintaining brand safety.

## Acceptance Criteria

**Given** I have uploaded images with AI-generated crop suggestions
**When** I interact with the cropping interface
**Then** Crop handles are draggable and resizable with mouse/touch
**And** Real-time preview shows crop results as I adjust
**And** Safe zone overlays indicate protected areas that cannot be cropped
**And** Aspect ratio constraints can be applied or removed
**And** Keyboard navigation supports precise 1px adjustments
**And** Crop adjustments trigger learning updates for Brand DNA
**And** Multiple crop presets are available (square, landscape, portrait)
**And** Undo/redo functionality supports experimentation
**And** Crop quality maintains image resolution without pixelation

## Implementation Notes

### Prerequisites
- Completed Story 2.4: Image Classification
- Object detection and classification results available
- WebAssembly processing pipeline functional
- Performance monitoring system active

### Technical Requirements
- Implement interactive crop overlay with drag/resize functionality
- Create real-time preview system
- Build safe zone overlay integration
- Design aspect ratio constraint system
- Implement keyboard navigation support
- Create undo/redo state management
- Build crop preset system
- Integrate with Brand DNA learning system

### Cropping Interface Architecture

#### Crop Types and Definitions
```typescript
export interface CropArea {
  id: string
  x: number
  y: number
  width: number
  height: number
  aspectRatio?: number
  rotation: number
  constraints?: CropConstraints
}

export interface CropConstraints {
  minWidth?: number
  maxWidth?: number
  minHeight?: number
  maxHeight?: number
  aspectRatio?: number
  maintainAspectRatio: boolean
  safeZones: SafeZone[]
}

export interface SafeZone {
  id: string
  type: 'face' | 'text' | 'logo'
  boundingBox: BoundingBox
  protectionLevel: 'hard' | 'soft'
  message: string
}

export type CropPreset = 'square' | 'landscape' | 'portrait' | 'custom'
export type CropHandle = 'nw' | 'ne' | 'se' | 'sw' | 'n' | 'e' | 's' | 'w' | 'center'
```

#### Crop Overlay Component
```typescript
interface CropOverlayProps {
  imageUrl: string
  imageWidth: number
  imageHeight: number
  initialCrop?: CropArea
  safeZones?: SafeZone[]
  onCropChange: (crop: CropArea) => void
  onCropComplete: (crop: CropArea) => void
  aspectRatio?: number
  showSafeZones?: boolean
  disabled?: boolean
}

export const CropOverlay: React.FC<CropOverlayProps> = ({
  imageUrl,
  imageWidth,
  imageHeight,
  initialCrop,
  safeZones = [],
  onCropChange,
  onCropComplete,
  aspectRatio,
  showSafeZones = true,
  disabled = false
}) => {
  const [crop, setCrop] = useState<CropArea>(initialCrop || {
    id: 'default',
    x: 0,
    y: 0,
    width: Math.min(imageWidth * 0.8, 400),
    height: Math.min(imageHeight * 0.8, 400)
  })
  
  const [isDragging, setIsDragging] = useState<string | null>(null)
  const [isResizing, setIsResizing] = useState<CropHandle | null>(null)
  const [dragStart, setDragStart] = useState({ x: 0, y: 0 })
  const [cropStart, setCropStart] = useState<CropArea | null>(null)
  
  const containerRef = useRef<HTMLDivElement>(null)
  const { trackInteraction } = usePerformanceContext()
  
  // Handle mouse down on crop area
  const handleMouseDown = useCallback((e: React.MouseEvent, handle?: CropHandle) => {
    if (disabled) return
    
    e.preventDefault()
    e.stopPropagation()
    
    const startX = e.clientX
    const startY = e.clientY
    
    if (handle) {
      setIsResizing(handle)
      setCropStart({ ...crop })
    } else {
      setIsDragging('move')
      setDragStart({ x: startX - crop.x, y: startY - crop.y })
      setCropStart({ ...crop })
    }
    
    trackInteraction('crop-interaction-start')
  }, [crop, disabled, trackInteraction])
  
  // Handle mouse move
  const handleMouseMove = useCallback((e: MouseEvent) => {
    if (!containerRef.current) return
    
    const rect = containerRef.current.getBoundingClientRect()
    const x = e.clientX - rect.left
    const y = e.clientY - rect.top
    
    if (isDragging === 'move' && cropStart) {
      const newX = Math.max(0, Math.min(x - dragStart.x, imageWidth - crop.width))
      const newY = Math.max(0, Math.min(y - dragStart.y, imageHeight - crop.height))
      
      const newCrop = { ...crop, x: newX, y: newY }
      setCrop(newCrop)
      onCropChange(newCrop)
    } else if (isResizing && cropStart) {
      const newCrop = calculateResize(cropStart, isResizing, x, y, imageWidth, imageHeight, aspectRatio)
      setCrop(newCrop)
      onCropChange(newCrop)
    }
  }, [isDragging, isResizing, cropStart, dragStart, imageWidth, imageHeight, aspectRatio, crop, onCropChange])
  
  // Handle mouse up
  const handleMouseUp = useCallback(() => {
    if (isDragging || isResizing) {
      setIsDragging(null)
      setIsResizing(null)
      setCropStart(null)
      onCropComplete(crop)
      trackInteraction('crop-interaction-end')
    }
  }, [isDragging, isResizing, crop, onCropComplete, trackInteraction])
  
  // Keyboard navigation
  const handleKeyDown = useCallback((e: KeyboardEvent) => {
    if (disabled) return
    
    const step = e.shiftKey ? 10 : 1 // Shift for faster movement
    let newCrop = { ...crop }
    
    switch (e.key) {
      case 'ArrowUp':
        newCrop.y = Math.max(0, crop.y - step)
        break
      case 'ArrowDown':
        newCrop.y = Math.min(imageHeight - crop.height, crop.y + step)
        break
      case 'ArrowLeft':
        newCrop.x = Math.max(0, crop.x - step)
        break
      case 'ArrowRight':
        newCrop.x = Math.min(imageWidth - crop.width, crop.x + step)
        break
      case 'Enter':
        onCropComplete(crop)
        return
      default:
        return
    }
    
    setCrop(newCrop)
    onCropChange(newCrop)
  }, [crop, imageWidth, imageHeight, onCropChange, onCropComplete])
  
  // Add global event listeners
  useEffect(() => {
    document.addEventListener('mousemove', handleMouseMove)
    document.addEventListener('mouseup', handleMouseUp)
    document.addEventListener('keydown', handleKeyDown)
    
    return () => {
      document.removeEventListener('mousemove', handleMouseMove)
      document.removeEventListener('mouseup', handleMouseUp)
      document.removeEventListener('keydown', handleKeyDown)
    }
  }, [handleMouseMove, handleMouseUp, handleKeyDown])
  
  const calculateResize = (startCrop: CropArea, handle: CropHandle, x: number, y: number, imgWidth: number, imgHeight: number, aspectRatio?: number): CropArea => {
    let { x, y, width, height } = startCrop
    
    switch (handle) {
      case 'nw':
        width = startCrop.width + (startCrop.x - x)
        height = startCrop.height + (startCrop.y - y)
        x = x
        y = y
        break
      case 'ne':
        width = x - startCrop.x
        height = startCrop.height + (startCrop.y - y)
        y = y
        break
      case 'se':
        width = x - startCrop.x
        height = y - startCrop.y
        break
      case 'sw':
        width = startCrop.width + (startCrop.x - x)
        height = y - startCrop.y
        x = x
        break
      case 'n':
        height = startCrop.height + (startCrop.y - y)
        y = y
        break
      case 's':
        height = y - startCrop.y
        break
      case 'e':
        width = x - startCrop.x
        break
      case 'w':
        width = startCrop.width + (startCrop.x - x)
        x = x
        break
    }
    
    // Apply aspect ratio constraint if specified
    if (aspectRatio) {
      if (handle.includes('e') || handle.includes('w')) {
        height = width / aspectRatio
      } else {
        width = height * aspectRatio
      }
    }
    
    // Ensure minimum size
    width = Math.max(50, width)
    height = Math.max(50, height)
    
    // Ensure within image bounds
    x = Math.max(0, Math.min(x, imgWidth - width))
    y = Math.max(0, Math.min(y, imgHeight - height))
    width = Math.min(width, imgWidth - x)
    height = Math.min(height, imgHeight - y)
    
    return { ...startCrop, x, y, width, height }
  }
  
  return (
    <div 
      ref={containerRef}
      className="relative inline-block"
      style={{ width: imageWidth, height: imageHeight }}
    >
      <img 
        src={imageUrl} 
        alt="Crop target"
        className="max-w-full h-auto"
        draggable={false}
      />
      
      {/* Safe Zone Overlays */}
      {showSafeZones && safeZones.map(zone => (
        <SafeZoneOverlay
          key={zone.id}
          zone={zone}
          crop={crop}
        />
      ))}
      
      {/* Crop Area */}
      <div
        className={`absolute border-2 ${disabled ? 'border-gray-600' : 'border-blue-500'} ${isDragging || isResizing ? 'cursor-move' : ''}`}
        style={{
          left: crop.x,
          top: crop.y,
          width: crop.width,
          height: crop.height
        }}
        onMouseDown={(e) => handleMouseDown(e)}
      >
        {/* Resize Handles */}
        {!disabled && (
          <>
            <CropHandle handle="nw" onMouseDown={(e) => handleMouseDown(e, 'nw')} />
            <CropHandle handle="ne" onMouseDown={(e) => handleMouseDown(e, 'ne')} />
            <CropHandle handle="se" onMouseDown={(e) => handleMouseDown(e, 'se')} />
            <CropHandle handle="sw" onMouseDown={(e) => handleMouseDown(e, 'sw')} />
            <CropHandle handle="n" onMouseDown={(e) => handleMouseDown(e, 'n')} />
            <CropHandle handle="e" onMouseDown={(e) => handleMouseDown(e, 'e')} />
            <CropHandle handle="s" onMouseDown={(e) => handleMouseDown(e, 's')} />
            <CropHandle handle="w" onMouseDown={(e) => handleMouseDown(e, 'w')} />
          </>
        )}
      </div>
      
      {/* Crop Preview */}
      <CropPreview
        originalImage={imageUrl}
        cropArea={crop}
        imageWidth={imageWidth}
        imageHeight={imageHeight}
      />
    </div>
  )
}

// Crop Handle Component
const CropHandle: React.FC<{ handle: CropHandle; onMouseDown: (e: React.MouseEvent) => void }> = ({ handle, onMouseDown }) => {
  const getPosition = () => {
    switch (handle) {
      case 'nw': return { top: -4, left: -4, cursor: 'nw-resize' }
      case 'ne': return { top: -4, right: -4, cursor: 'ne-resize' }
      case 'se': return { bottom: -4, right: -4, cursor: 'se-resize' }
      case 'sw': return { bottom: -4, left: -4, cursor: 'sw-resize' }
      case 'n': return { top: -4, left: '50%', transform: 'translateX(-50%)', cursor: 'n-resize' }
      case 'e': return { top: '50%', right: -4, transform: 'translateY(-50%)', cursor: 'e-resize' }
      case 's': return { bottom: -4, left: '50%', transform: 'translateX(-50%)', cursor: 's-resize' }
      case 'w': return { top: '50%', left: -4, transform: 'translateY(-50%)', cursor: 'w-resize' }
      default: return {}
    }
  }
  
  const pos = getPosition()
  
  return (
    <div
      className="absolute w-3 h-3 bg-blue-500 border border-white rounded-full"
      style={{
        ...pos,
        cursor: pos.cursor as string
      }}
      onMouseDown={onMouseDown}
    />
  )
}
```

#### Safe Zone Overlay Component
```typescript
interface SafeZoneOverlayProps {
  zone: SafeZone
  crop: CropArea
}

export const SafeZoneOverlay: React.FC<SafeZoneOverlayProps> = ({ zone, crop }) => {
  const isViolation = checkCropViolation(crop, zone)
  
  const getZoneStyle = () => {
    const baseStyle = {
      position: 'absolute' as const,
      border: '2px dashed',
      backgroundColor: zone.protectionLevel === 'hard' ? 'rgba(239, 68, 68, 0.2)' : 'rgba(245, 158, 11, 0.2)'
    }
    
    if (isViolation) {
      return {
        ...baseStyle,
        borderColor: '#ef4444',
        backgroundColor: 'rgba(239, 68, 68, 0.3)'
      }
    }
    
    return {
      ...baseStyle,
      borderColor: zone.type === 'face' ? '#10b981' : zone.type === 'text' ? '#f59e0b' : '#ef4444'
    }
  }
  
  return (
    <div
      style={{
        ...getZoneStyle(),
        left: zone.boundingBox.x,
        top: zone.boundingBox.y,
        width: zone.boundingBox.width,
        height: zone.boundingBox.height
      }}
      title={zone.message}
    >
      {isViolation && (
        <div className="absolute -top-6 -right-2 bg-red-500 text-white text-xs px-2 py-1 rounded">
          ⚠️ Protected
        </div>
      )}
    </div>
  )
}

const checkCropViolation = (crop: CropArea, zone: SafeZone): boolean => {
  const cropRect = {
    left: crop.x,
    top: crop.y,
    right: crop.x + crop.width,
    bottom: crop.y + crop.height
  }
  
  const zoneRect = {
    left: zone.boundingBox.x,
    top: zone.boundingBox.y,
    right: zone.boundingBox.x + zone.boundingBox.width,
    bottom: zone.boundingBox.y + zone.boundingBox.height
  }
  
  if (zone.protectionLevel === 'hard') {
    // Hard protection: any overlap is violation
    return !(
      cropRect.right <= zoneRect.left ||
      cropRect.left >= zoneRect.right ||
      cropRect.bottom <= zoneRect.top ||
      cropRect.top >= zoneRect.bottom
    )
  } else {
    // Soft protection: only violation if crop covers > 50% of zone
    const overlapWidth = Math.min(cropRect.right, zoneRect.right) - Math.max(cropRect.left, zoneRect.left)
    const overlapHeight = Math.min(cropRect.bottom, zoneRect.bottom) - Math.max(cropRect.top, zoneRect.top)
    const overlapArea = overlapWidth * overlapHeight
    const zoneArea = zone.boundingBox.width * zone.boundingBox.height
    
    return overlapArea > zoneArea * 0.5
  }
}
```

#### Crop Preset System
```typescript
interface CropPresetConfig {
  name: CropPreset
  label: string
  icon: string
  aspectRatio: number
  description: string
}

export const CROP_PRESETS: CropPresetConfig[] = [
  {
    name: 'square',
    label: 'Square',
    icon: '⬜',
    aspectRatio: 1,
    description: 'Perfect square crop for social media'
  },
  {
    name: 'landscape',
    label: 'Landscape',
    icon: '🖼️',
    aspectRatio: 16/9,
    description: 'Wide landscape format for displays'
  },
  {
    name: 'portrait',
    label: 'Portrait',
    icon: '🖼️',
    aspectRatio: 9/16,
    description: 'Tall portrait format for mobile'
  }
]

export const CropPresetSelector: React.FC<{
  selectedPreset: CropPreset
  onPresetChange: (preset: CropPreset) => void
  disabled?: boolean
}> = ({ selectedPreset, onPresetChange, disabled }) => {
  return (
    <div className="flex space-x-2">
      {CROP_PRESETS.map(preset => (
        <button
          key={preset.name}
          onClick={() => onPresetChange(preset.name)}
          disabled={disabled}
          className={`px-3 py-2 rounded-lg border transition-colors ${
            selectedPreset === preset.name
              ? 'bg-blue-600 border-blue-600 text-white'
              : 'bg-gray-800 border-gray-700 text-gray-300 hover:bg-gray-700'
          } ${disabled ? 'opacity-50 cursor-not-allowed' : ''}`}
          title={preset.description}
        >
          <span className="mr-2">{preset.icon}</span>
          <span className="text-sm">{preset.label}</span>
        </button>
      ))}
    </div>
  )
}
```

### Undo/Redo System
```typescript
interface CropHistory {
  past: CropArea[]
  present: CropArea
  future: CropArea[]
}

export class CropHistoryManager {
  private history: CropHistory = {
    past: [],
    present: { id: 'default', x: 0, y: 0, width: 200, height: 200 },
    future: []
  }
  
  push(crop: CropArea): void {
    this.history.past.push(this.history.present)
    this.history.present = crop
    this.history.future = [] // Clear future when new action is taken
  }
  
  undo(): CropArea | null {
    if (this.history.past.length === 0) return null
    
    const previous = this.history.past.pop()!
    this.history.future.unshift(this.history.present)
    this.history.present = previous
    
    return previous
  }
  
  redo(): CropArea | null {
    if (this.history.future.length === 0) return null
    
    const next = this.history.future.shift()!
    this.history.past.push(this.history.present)
    this.history.present = next
    
    return next
  }
  
  canUndo(): boolean {
    return this.history.past.length > 0
  }
  
  canRedo(): boolean {
    return this.history.future.length > 0
  }
  
  getCurrent(): CropArea {
    return this.history.present
  }
}
```

### File Structure Changes
```
pick-sizer/
├── src/
│   ├── components/
│   │   ├── cropping/
│   │   │   ├── crop-overlay.tsx (new)
│   │   │   ├── crop-handle.tsx (new)
│   │   │   ├── safe-zone-overlay.tsx (new)
│   │   │   ├── crop-preview.tsx (new)
│   │   │   ├── crop-preset-selector.tsx (new)
│   │   │   └── crop-history.tsx (new)
│   │   └── ui/
│   │       └── aspect-ratio-lock.tsx (new)
│   ├── hooks/
│   │   ├── use-crop-overlay.ts (new)
│   │   ├── use-crop-history.ts (new)
│   │   └── use-keyboard-cropping.ts (new)
│   ├── types/
│   │   └── cropping.ts (new)
│   └── utils/
│       ├── crop-utils.ts (new)
│       └── geometry-utils.ts (new)
```

### Performance Requirements
- Crop handle response < 50ms
- Real-time preview updates at 60fps
- Keyboard navigation response < 16ms
- Undo/redo operations < 10ms
- Memory usage < 50MB for crop state
- Touch interaction response < 100ms

### Brand DNA Learning Integration
```typescript
interface CropLearningData {
  imageId: string
  originalCrop: CropArea
  adjustedCrop: CropArea
  classification: ImageCategory
  detectedObjects: DetectedObject[]
  timestamp: number
}

export const recordCropAdjustment = async (learningData: CropLearningData): Promise<void> => {
  // Calculate adjustment delta
  const delta = {
    x: learningData.adjustedCrop.x - learningData.originalCrop.x,
    y: learningData.adjustedCrop.y - learningData.originalCrop.y,
    width: learningData.adjustedCrop.width - learningData.originalCrop.width,
    height: learningData.adjustedCrop.height - learningData.originalCrop.height
  }
  
  // Update Brand DNA based on adjustment pattern
  await updateBrandDNAPreferences(learningData.classification, delta)
  
  // Store learning data for future training
  await persistCropLearning(learningData)
}

const updateBrandDNAPreferences = async (category: ImageCategory, delta: any): Promise<void> => {
  // Update brand-specific crop preferences
  // Adjust padding rules
  // Modify composition bias
  // Update safe zone sensitivity
}
```

## Definition of Done

- [ ] Interactive crop overlay implemented
- [ ] Drag and resize functionality working
- [ ] Safe zone overlay integration complete
- [ ] Real-time preview system functional
- [ ] Aspect ratio constraints implemented
- [ ] Keyboard navigation support added
- [ ] Crop preset system created
- [ ] Undo/redo functionality working
- [ ] Brand DNA learning integration
- [ ] Performance requirements met
- [ ] Touch interaction support
- [ ] Error handling complete
- [ ] Unit tests for cropping components
- [ ] Integration tests for crop workflow
- [ ] UI tests for crop interaction
- [ ] Documentation updated

## Risks & Mitigations

**Risk**: Complex interaction causing performance issues
**Mitigation**: Optimize render cycles and use requestAnimationFrame

**Risk**: Accidental crop of protected areas
**Mitigation**: Implement clear safe zone warnings and hard protection

**Risk**: Undo/redo state becoming too large
**Mitigation**: Limit history size and implement memory management

## Related Stories

- **Story 2.4**: Image Classification (prerequisite)
- **Story 4.3**: Safe Zone Overlays (will enhance this functionality)
- **Story 3.2**: Silent Learning Capture (will use crop adjustment data)

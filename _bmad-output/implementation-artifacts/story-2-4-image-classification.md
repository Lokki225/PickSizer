---
story_id: "2-4"
story_title: "Image Classification"
epic: "Epic 2: Core Image Processing Engine"
status: "ready-for-dev"
priority: "high"
assigned_to: ""
estimated_hours: 8
created_date: "2026-01-16"
updated_date: "2026-01-16"
tags: ["ai", "classification", "machine-learning", "image-analysis"]
---

# Story 2.4: Image Classification

As a user,
I want to see automatic image classification (Lifestyle, Product, Portrait),
So that system can apply appropriate brand DNA rules and cropping strategies.

## Acceptance Criteria

**Given** I have uploaded images and object detection is complete
**When** image classification engine analyzes each image
**Then** Images are classified into Lifestyle, Product, or Portrait categories
**And** Classification confidence scores are displayed for each image
**And** Classification results appear within 300ms of processing
**And** Misclassifications can be corrected through user feedback
**And** Classification accuracy improves through learning from corrections
**And** Different crop strategies are applied based on classification
**And** Classification results influence Brand DNA rule application
**And** Batch processing maintains consistent classification across similar images

## Implementation Notes

### Prerequisites
- Completed Story 2.3: Semantic Object Detection
- Object detection pipeline functional
- Detection results available for classification
- Performance monitoring system active

### Technical Requirements
- Implement image classification using machine learning models
- Create confidence scoring system for classifications
- Build user feedback mechanism for corrections
- Design classification visualization components
- Implement learning system from user corrections
- Integrate with Brand DNA rule application
- Support batch classification with consistent results

### Classification Engine Architecture

#### Classification Types and Definitions
```typescript
export type ImageCategory = 'lifestyle' | 'product' | 'portrait'

export interface ClassificationResult {
  category: ImageCategory
  confidence: number
  alternativeCategories: Array<{
    category: ImageCategory
    confidence: number
  }>
  metadata: {
    dominantColors: string[]
    composition: string
    lighting: string
    subjectCount: number
  }
  processingTime: number
}

export interface ClassificationFeatures {
  colorHistogram: number[]
  edgeDistribution: number[]
  textureFeatures: number[]
  shapeFeatures: number[]
  objectPresence: Record<string, number>
}
```

#### Image Classifier Implementation
```typescript
export class ImageClassifier {
  private model: any // TensorFlow.js or similar ML model
  private featureExtractor: FeatureExtractor
  
  async classify(imageData: ImageData, detectedObjects?: DetectedObject[]): Promise<ClassificationResult> {
    const startTime = performance.now()
    
    try {
      // Extract features from image
      const features = await this.extractFeatures(imageData, detectedObjects)
      
      // Run classification model
      const rawPrediction = await this.model.predict(features)
      
      // Process and format results
      const result = this.processPrediction(rawPrediction, detectedObjects)
      
      const processingTime = performance.now() - startTime
      
      return {
        ...result,
        processingTime
      }
    } catch (error) {
      console.error('Image classification failed:', error)
      throw error
    }
  }
  
  private async extractFeatures(imageData: ImageData, detectedObjects?: DetectedObject[]): Promise<ClassificationFeatures> {
    // Color histogram analysis
    const colorHistogram = this.extractColorHistogram(imageData)
    
    // Edge detection and distribution
    const edgeDistribution = this.extractEdgeFeatures(imageData)
    
    // Texture analysis using local binary patterns
    const textureFeatures = this.extractTextureFeatures(imageData)
    
    // Shape and composition features
    const shapeFeatures = this.extractShapeFeatures(imageData)
    
    // Object presence features from detection results
    const objectPresence = this.extractObjectFeatures(detectedObjects || [])
    
    return {
      colorHistogram,
      edgeDistribution,
      textureFeatures,
      shapeFeatures,
      objectPresence
    }
  }
  
  private extractColorHistogram(imageData: ImageData): number[] {
    // Convert to HSV and create histogram
    const histogram = new Array(180).fill(0) // Hue bins
    
    for (let i = 0; i < imageData.data.length; i += 4) {
      const r = imageData.data[i]
      const g = imageData.data[i + 1]
      const b = imageData.data[i + 2]
      
      const hsv = this.rgbToHsv(r, g, b)
      const hueIndex = Math.floor(hsv.h * 2) // 0-179 range
      histogram[hueIndex]++
    }
    
    // Normalize histogram
    const total = imageData.data.length / 4
    return histogram.map(count => count / total)
  }
  
  private extractEdgeFeatures(imageData: ImageData): number[] {
    // Implement edge detection using Sobel or Canny
    const edges = this.detectEdges(imageData)
    
    // Analyze edge distribution
    const horizontalEdges = edges.filter(e => e.angle >= -45 && e.angle < 45).length
    const verticalEdges = edges.filter(e => e.angle >= 45 && e.angle < 135).length
    const diagonalEdges = edges.length - horizontalEdges - verticalEdges
    
    return [
      horizontalEdges / edges.length,
      verticalEdges / edges.length,
      diagonalEdges / edges.length,
      edges.length / (imageData.width * imageData.height)
    ]
  }
  
  private processPrediction(rawPrediction: any, detectedObjects?: DetectedObject[]): Omit<ClassificationResult, 'processingTime'> {
    // Convert model output to standardized format
    const probabilities = rawPrediction.data || rawPrediction
    
    // Sort categories by confidence
    const sortedCategories = Object.entries(probabilities)
      .sort(([, a], [, b]) => b - a)
      .map(([category, confidence]) => ({
        category: category as ImageCategory,
        confidence
      }))
    
    const primary = sortedCategories[0]
    const alternatives = sortedCategories.slice(1, 3) // Top 3 alternatives
    
    // Extract metadata
    const metadata = this.extractMetadata(probabilities, detectedObjects)
    
    return {
      category: primary.category,
      confidence: primary.confidence,
      alternativeCategories: alternatives,
      metadata
    }
  }
  
  private extractMetadata(probabilities: Record<string, number>, detectedObjects?: DetectedObject[]) {
    // Determine dominant colors from color analysis
    const dominantColors = this.getDominantColors(probabilities)
    
    // Analyze composition based on object positions
    const composition = this.analyzeComposition(detectedObjects || [])
    
    // Assess lighting conditions
    const lighting = this.assessLighting(probabilities)
    
    // Count subjects (people, products)
    const subjectCount = this.countSubjects(detectedObjects || [])
    
    return {
      dominantColors,
      composition,
      lighting,
      subjectCount
    }
  }
}
```

#### Classification Rules Engine
```typescript
export interface ClassificationRule {
  category: ImageCategory
  conditions: RuleCondition[]
  cropStrategy: CropStrategy
  brandDNAInfluence: BrandDNARule[]
}

export interface RuleCondition {
  feature: string
  operator: 'gt' | 'lt' | 'eq' | 'contains'
  value: any
  weight: number
}

export class ClassificationRulesEngine {
  private rules: ClassificationRule[] = [
    {
      category: 'portrait',
      conditions: [
        { feature: 'faceCount', operator: 'gt', value: 0, weight: 0.8 },
        { feature: 'centerComposition', operator: 'eq', value: 'centered', weight: 0.6 },
        { feature: 'backgroundComplexity', operator: 'lt', value: 0.3, weight: 0.4 }
      ],
      cropStrategy: 'face-focused',
      brandDNAInfluence: ['facial-protection', 'headroom-rules']
    },
    {
      category: 'product',
      conditions: [
        { feature: 'logoCount', operator: 'gt', value: 0, weight: 0.7 },
        { feature: 'textPresence', operator: 'eq', value: true, weight: 0.5 },
        { feature: 'centeredSubject', operator: 'eq', value: true, weight: 0.6 }
      ],
      cropStrategy: 'product-focused',
      brandDNAInfluence: ['brand-logo-protection', 'text-clearance']
    },
    {
      category: 'lifestyle',
      conditions: [
        { feature: 'multipleSubjects', operator: 'eq', value: true, weight: 0.6 },
        { feature: 'environmentalContext', operator: 'contains', value: 'indoor-outdoor', weight: 0.5 },
        { feature: 'colorHarmony', operator: 'gt', value: 0.7, weight: 0.4 }
      ],
      cropStrategy: 'balanced',
      brandDNAInfluence: ['composition-rules', 'color-balance']
    }
  ]
  
  evaluateRule(features: ClassificationFeatures, detectedObjects: DetectedObject[]): ClassificationRule | null {
    for (const rule of this.rules) {
      let score = 0
      let totalWeight = 0
      
      for (const condition of rule.conditions) {
        const featureValue = this.extractFeatureValue(features, detectedObjects, condition.feature)
        const conditionMet = this.evaluateCondition(featureValue, condition)
        
        if (conditionMet) {
          score += condition.weight
        }
        totalWeight += condition.weight
      }
      
      if (score / totalWeight > 0.7) { // 70% confidence threshold
        return rule
      }
    }
    
    return null
  }
  
  private evaluateCondition(value: any, condition: RuleCondition): boolean {
    switch (condition.operator) {
      case 'gt': return value > condition.value
      case 'lt': return value < condition.value
      case 'eq': return value === condition.value
      case 'contains': return String(value).includes(String(condition.value))
      default: return false
    }
  }
}
```

### Classification Visualization Component
```typescript
interface ClassificationDisplayProps {
  result: ClassificationResult
  imageUrl: string
  onCorrection?: (correctCategory: ImageCategory) => void
  showAlternatives?: boolean
}

export const ClassificationDisplay: React.FC<ClassificationDisplayProps> = ({
  result,
  imageUrl,
  onCorrection,
  showAlternatives = true
}) => {
  const [isCorrecting, setIsCorrecting] = useState(false)
  
  const getCategoryColor = (category: ImageCategory) => {
    switch (category) {
      case 'portrait': return '#10b981' // Green
      case 'product': return '#f59e0b' // Amber
      case 'lifestyle': return '#3b82f6' // Blue
      default: return '#6b7280' // Gray
    }
  }
  
  const getCategoryIcon = (category: ImageCategory) => {
    switch (category) {
      case 'portrait': return '👤'
      case 'product': return '📦'
      case 'lifestyle': return '🌅'
      default: return '❓'
    }
  }
  
  return (
    <div className="bg-gray-900 rounded-lg p-4 border border-gray-800">
      <div className="flex items-center justify-between mb-3">
        <h4 className="text-sm font-medium text-gray-300">Image Classification</h4>
        <span className="text-xs text-gray-500">
          {result.processingTime.toFixed(1)}ms
        </span>
      </div>
      
      {/* Primary Classification */}
      <div className="flex items-center space-x-3 mb-4">
        <div 
          className="w-12 h-12 rounded-full flex items-center justify-center text-lg"
          style={{ backgroundColor: getCategoryColor(result.category) + '20' }}
        >
          <span>{getCategoryIcon(result.category)}</span>
        </div>
        
        <div className="flex-1">
          <div className="flex items-center space-x-2">
            <span 
              className="font-medium capitalize"
              style={{ color: getCategoryColor(result.category) }}
            >
              {result.category}
            </span>
            <span className="text-sm text-gray-400">
              {Math.round(result.confidence * 100)}% confidence
            </span>
          </div>
          
          <div className="w-full bg-gray-800 rounded-full h-2 mt-1">
            <div 
              className="h-2 rounded-full transition-all duration-300"
              style={{ 
                width: `${result.confidence * 100}%`,
                backgroundColor: getCategoryColor(result.category)
              }}
            />
          </div>
        </div>
      </div>
      
      {/* Alternative Classifications */}
      {showAlternatives && result.alternativeCategories.length > 0 && (
        <div className="space-y-2 mb-4">
          <h5 className="text-xs font-medium text-gray-400">Alternative classifications:</h5>
          {result.alternativeCategories.map((alt, index) => (
            <div key={alt.category} className="flex items-center justify-between">
              <span className="text-sm text-gray-300 capitalize flex items-center space-x-2">
                <span>{getCategoryIcon(alt.category)}</span>
                <span>{alt.category}</span>
              </span>
              <span className="text-xs text-gray-500">
                {Math.round(alt.confidence * 100)}%
              </span>
            </div>
          ))}
        </div>
      )}
      
      {/* Metadata */}
      <div className="grid grid-cols-2 gap-3 mb-4">
        <div>
          <span className="text-xs text-gray-500">Dominant colors:</span>
          <div className="flex space-x-1 mt-1">
            {result.metadata.dominantColors.slice(0, 4).map((color, index) => (
              <div 
                key={index}
                className="w-4 h-4 rounded border border-gray-700"
                style={{ backgroundColor: color }}
                title={color}
              />
            ))}
          </div>
        </div>
        
        <div>
          <span className="text-xs text-gray-500">Composition:</span>
          <span className="text-sm text-gray-300 capitalize">
            {result.metadata.composition}
          </span>
        </div>
        
        <div>
          <span className="text-xs text-gray-500">Lighting:</span>
          <span className="text-sm text-gray-300 capitalize">
            {result.metadata.lighting}
          </span>
        </div>
        
        <div>
          <span className="text-xs text-gray-500">Subjects:</span>
          <span className="text-sm text-gray-300">
            {result.metadata.subjectCount}
          </span>
        </div>
      </div>
      
      {/* Correction Interface */}
      {onCorrection && (
        <div className="flex items-center space-x-2">
          <span className="text-sm text-gray-400">Incorrect classification?</span>
          <button
            onClick={() => setIsCorrecting(true)}
            className="text-sm text-blue-400 hover:text-blue-300"
          >
            Correct it
          </button>
          
          {isCorrecting && (
            <div className="flex space-x-2">
              {(['portrait', 'product', 'lifestyle'] as ImageCategory[]).map(category => (
                <button
                  key={category}
                  onClick={() => {
                    onCorrection(category)
                    setIsCorrecting(false)
                  }}
                  className="px-2 py-1 text-xs rounded border border-gray-700 hover:border-gray-600"
                  style={{ 
                    backgroundColor: category === result.category ? getCategoryColor(category) + '20' : 'transparent'
                  }}
                >
                  {getCategoryIcon(category)} {category}
                </button>
              ))}
            </div>
          )}
        </div>
      )}
    </div>
  )
}
```

### File Structure Changes
```
pick-sizer/
├── src/
│   ├── components/
│   │   ├── classification/
│   │   │   ├── classification-display.tsx (new)
│   │   │   ├── category-badge.tsx (new)
│   │   │   └── confidence-meter.tsx (new)
│   │   └── ai/
│   │       ├── image-classifier.ts (new)
│   │       ├── feature-extractor.ts (new)
│   │       └── classification-rules.ts (new)
│   ├── hooks/
│   │   ├── use-image-classification.ts (new)
│   │   └── use-classification-feedback.ts (new)
│   ├── types/
│   │   └── classification.ts (new)
│   └── utils/
│       ├── feature-extraction.ts (new)
│       └── classification-utils.ts (new)
```

### Performance Requirements
- Single image classification < 300ms
- Batch classification (50 images) < 5 seconds
- Memory usage < 100MB
- Model loading time < 1 second
- UI updates maintain 60fps

### Learning System Integration
```typescript
interface ClassificationFeedback {
  imageId: string
  originalClassification: ClassificationResult
  userCorrection: ImageCategory
  timestamp: number
  features: ClassificationFeatures
}

export class ClassificationLearningSystem {
  private feedbackHistory: ClassificationFeedback[] = []
  
  async recordFeedback(feedback: ClassificationFeedback): Promise<void> {
    this.feedbackHistory.push(feedback)
    
    // Update classification model
    await this.updateModel(feedback)
    
    // Adjust classification rules
    await this.updateRules(feedback)
    
    // Store for training
    await this.persistFeedback(feedback)
  }
  
  private async updateModel(feedback: ClassificationFeedback): Promise<void> {
    // Fine-tune model with corrected examples
    // Adjust feature weights
    // Update decision boundaries
  }
  
  getClassificationAccuracy(): number {
    const total = this.feedbackHistory.length
    const correct = this.feedbackHistory.filter(f => 
      f.originalClassification.category === f.userCorrection
    ).length
    return total > 0 ? correct / total : 0
  }
}
```

## Definition of Done

- [ ] Image classification model implemented
- [ ] Feature extraction system functional
- [ ] Classification rules engine created
- [ ] Classification visualization component built
- [ ] User feedback mechanism implemented
- [ ] Learning system integration complete
- [ ] Performance requirements met
- [ ] Integration with Brand DNA system
- [ ] Unit tests for classification pipeline
- [ ] Integration tests for classification workflow
- [ ] UI tests for classification display
- [ ] Documentation updated

## Risks & Mitigations

**Risk**: Misclassification of complex images
**Mitigation**: Implement confidence thresholds and user feedback system

**Risk**: Bias in classification model
**Mitigation**: Use diverse training data and regular model evaluation

**Risk**: Performance impact with complex feature extraction
**Mitigation**: Optimize algorithms and use WebAssembly for intensive operations

## Related Stories

- **Story 2.3**: Semantic Object Detection (prerequisite)
- **Story 2.5**: Basic Cropping Interface (next step)
- **Story 3.2**: Silent Learning Capture (will use classification feedback)

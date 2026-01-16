---
story_id: "1-4"
story_title: "Performance Monitoring & Skeleton UI Foundation"
epic: "Epic 1: Instant Readiness & Performance Foundation"
status: "review"
priority: "high"
assigned_to: ""
estimated_hours: 5
created_date: "2026-01-16"
updated_date: "2026-01-16"
tags: ["infrastructure", "performance", "ui", "monitoring"]
---

# Story 1.4: Performance Monitoring & Skeleton UI Foundation

As a developer,
I want to set up performance monitoring and skeleton UI components,
So that I can ensure <300ms Interactive Pulse and provide visual feedback during processing.

## Acceptance Criteria

**Given** I have the Next.js project with WebAssembly integration
**When** I implement performance monitoring and skeleton UI components
**Then** Performance monitoring tracks UI response times and WebAssembly processing duration
**And** Skeleton UI components are created for image processing states
**And** Loading states provide visual feedback within 100ms of user interaction
**And** Performance metrics are logged for debugging and optimization
**And** Skeleton components match the "Silent Cockpit" design specifications
**And** Error boundaries are configured to handle WebAssembly failures gracefully

### Completion Notes
✅ Successfully implemented Performance Monitoring & Skeleton UI Foundation:
- Created comprehensive performance monitoring system with hooks and context
- Implemented skeleton UI components for various loading states
- Set up error boundaries for graceful WebAssembly failure handling
- Added performance metrics tracking with <300ms Interactive Pulse compliance
- Applied Silent Cockpit design patterns with dark theme
- Created performance metrics dashboard component
- Integrated all components into application with error boundaries
- Development server runs successfully with performance monitoring
- All core functionality working as expected

### File List
- src/hooks/use-performance.ts (new)
- src/hooks/use-webassembly.ts (new)
- src/components/providers/performance-provider.tsx (new)
- src/components/ui/image-skeleton.tsx (new)
- src/components/ui/processing-skeleton.tsx (new)
- src/components/ui/error-boundary.tsx (new)
- src/components/ui/performance-metrics.tsx (new)
- src/types/wasm.d.ts (new)
- src/__tests__/performance.test.tsx (new)
- src/app/page.tsx (modified)

### Change Log
- 2026-01-16: Implemented Performance Monitoring & Skeleton UI Foundation
  - Added performance monitoring hooks and context
  - Created skeleton UI components for loading states
  - Implemented error boundaries for WebAssembly failures
  - Added performance metrics dashboard
  - Applied Silent Cockpit design patterns
  - Integrated performance monitoring throughout application

## Implementation Notes

### Prerequisites
- Completed Story 1.3 (WebAssembly Build Pipeline)
- Next.js project with TypeScript
- Tailwind CSS configured

### Technical Requirements
- Implement performance monitoring system
- Create skeleton UI components for various loading states
- Set up error boundaries for WebAssembly failures
- Configure performance logging and metrics
- Ensure <300ms Interactive Pulse compliance
- Follow Silent Cockpit design patterns

### Performance Monitoring Setup

#### Performance Hook (hooks/usePerformance.ts)
```typescript
interface PerformanceMetrics {
  uiResponseTime: number;
  wasmProcessingTime: number;
  totalProcessingTime: number;
  memoryUsage: number;
}

export const usePerformance = () => {
  const [metrics, setMetrics] = useState<PerformanceMetrics>({
    uiResponseTime: 0,
    wasmProcessingTime: 0,
    totalProcessingTime: 0,
    memoryUsage: 0,
  });

  const startTimer = useCallback((label: string) => {
    performance.mark(`${label}-start`);
  }, []);

  const endTimer = useCallback((label: string) => {
    performance.mark(`${label}-end`);
    performance.measure(label, `${label}-start`, `${label}-end`);
    const measure = performance.getEntriesByName(label)[0];
    return measure.duration;
  }, []);

  return { metrics, startTimer, endTimer };
};
```

### Skeleton UI Components

#### ImageSkeleton Component
```typescript
interface ImageSkeletonProps {
  width?: number;
  height?: number;
  className?: string;
}

export const ImageSkeleton: React.FC<ImageSkeletonProps> = ({
  width = 200,
  height = 200,
  className = "",
}) => {
  return (
    <div 
      className={cn(
        "animate-pulse bg-gray-800 rounded-lg",
        "border border-gray-700",
        className
      )}
      style={{ width, height }}
    >
      <div className="flex items-center justify-center h-full">
        <div className="w-8 h-8 border-2 border-gray-600 border-t-blue-500 rounded-full animate-spin" />
      </div>
    </div>
  );
};
```

#### ProcessingSkeleton Component
```typescript
interface ProcessingSkeletonProps {
  stage: 'uploading' | 'analyzing' | 'processing' | 'complete';
  progress?: number;
}

export const ProcessingSkeleton: React.FC<ProcessingSkeletonProps> = ({
  stage,
  progress = 0,
}) => {
  const stages = [
    { key: 'uploading', label: 'Uploading', icon: '📤' },
    { key: 'analyzing', label: 'Analyzing', icon: '🔍' },
    { key: 'processing', label: 'Processing', icon: '⚡' },
    { key: 'complete', label: 'Complete', icon: '✅' },
  ];

  const currentStageIndex = stages.findIndex(s => s.key === stage);

  return (
    <div className="bg-gray-900 rounded-lg p-4 border border-gray-800">
      <div className="flex items-center justify-between mb-3">
        <h3 className="text-sm font-medium text-gray-300">Processing Image</h3>
        <span className="text-xs text-gray-500">{Math.round(progress)}%</span>
      </div>
      
      <div className="space-y-2">
        {stages.map((s, index) => (
          <div 
            key={s.key}
            className={cn(
              "flex items-center space-x-2 text-xs",
              index <= currentStageIndex ? "text-blue-400" : "text-gray-600"
            )}
          >
            <span className="text-lg">{s.icon}</span>
            <span>{s.label}</span>
            {index < currentStageIndex && (
              <span className="text-green-400">✓</span>
            )}
          </div>
        ))}
      </div>
      
      <div className="mt-3 w-full bg-gray-800 rounded-full h-1">
        <div 
          className="bg-blue-500 h-1 rounded-full transition-all duration-300"
          style={{ width: `${progress}%` }}
        />
      </div>
    </div>
  );
};
```

### Error Boundary Setup

#### WebAssemblyErrorBoundary Component
```typescript
interface WebAssemblyErrorBoundaryProps {
  children: React.ReactNode;
  fallback?: React.ComponentType<{ error: Error; retry: () => void }>;
}

export const WebAssemblyErrorBoundary: React.FC<WebAssemblyErrorBoundaryProps> = ({
  children,
  fallback: FallbackComponent = DefaultErrorFallback,
}) => {
  return (
    <ErrorBoundary
      fallback={FallbackComponent}
      onError={(error, errorInfo) => {
        console.error('WebAssembly Error:', error, errorInfo);
        // Log to monitoring service
      }}
    >
      {children}
    </ErrorBoundary>
  );
};

const DefaultErrorFallback: React.FC<{ error: Error; retry: () => void }> = ({
  error,
  retry,
}) => {
  return (
    <div className="bg-red-900/20 border border-red-800 rounded-lg p-4">
      <div className="flex items-center space-x-2 mb-2">
        <span className="text-red-400">⚠️</span>
        <h3 className="text-red-300 font-medium">Processing Error</h3>
      </div>
      <p className="text-red-400 text-sm mb-3">
        {error.message || 'An error occurred during image processing'}
      </p>
      <button
        onClick={retry}
        className="bg-red-800 hover:bg-red-700 text-white px-3 py-1 rounded text-sm"
      >
        Retry
      </button>
    </div>
  );
};
```

### Performance Monitoring Integration

#### Performance Provider
```typescript
interface PerformanceContextType {
  trackInteraction: (name: string) => void;
  trackWebAssemblyProcessing: (duration: number) => void;
  getMetrics: () => PerformanceMetrics;
}

const PerformanceContext = createContext<PerformanceContextType | null>(null);

export const PerformanceProvider: React.FC<{ children: React.ReactNode }> = ({
  children,
}) => {
  const [metrics, setMetrics] = useState<PerformanceMetrics>({
    uiResponseTime: 0,
    wasmProcessingTime: 0,
    totalProcessingTime: 0,
    memoryUsage: 0,
  });

  const trackInteraction = useCallback((name: string) => {
    const start = performance.now();
    return () => {
      const duration = performance.now() - start;
      setMetrics(prev => ({
        ...prev,
        uiResponseTime: duration,
      }));
      
      // Log if exceeds 300ms threshold
      if (duration > 300) {
        console.warn(`Slow interaction detected: ${name} took ${duration.toFixed(2)}ms`);
      }
    };
  }, []);

  const trackWebAssemblyProcessing = useCallback((duration: number) => {
    setMetrics(prev => ({
      ...prev,
      wasmProcessingTime: duration,
      totalProcessingTime: prev.uiResponseTime + duration,
    }));
  }, []);

  return (
    <PerformanceContext.Provider value={{
      trackInteraction,
      trackWebAssemblyProcessing,
      getMetrics: () => metrics,
    }}>
      {children}
    </PerformanceContext.Provider>
  );
};
```

### Directory Structure
```
pick-sizer/
├── components/
│   ├── ui/
│   │   ├── skeleton.tsx (new)
│   │   ├── image-skeleton.tsx (new)
│   │   ├── processing-skeleton.tsx (new)
│   │   └── error-boundary.tsx (new)
│   └── providers/
│       └── performance-provider.tsx (new)
├── hooks/
│   ├── use-performance.ts (new)
│   └── use-webassembly.ts (new)
└── utils/
    └── performance-monitoring.ts (new)
```

## Definition of Done

- [x] Performance monitoring system implemented
- [x] Skeleton UI components created for all loading states
- [x] Error boundaries configured for WebAssembly failures
- [x] Performance metrics logging functional
- [x] <300ms Interactive Pulse compliance verified
- [x] Silent Cockpit design patterns applied
- [x] Components tested with various loading scenarios
- [x] Documentation updated with performance guidelines

## Performance Tests

```typescript
describe('Performance Requirements', () => {
  test('UI response time < 300ms', async () => {
    const endInteraction = trackInteraction('button-click');
    // Simulate user interaction
    await new Promise(resolve => setTimeout(resolve, 50));
    const duration = endInteraction();
    expect(duration).toBeLessThan(300);
  });

  test('Skeleton appears within 100ms', async () => {
    const startTime = performance.now();
    // Trigger loading state
    render(<ImageSkeleton />);
    const renderTime = performance.now() - startTime;
    expect(renderTime).toBeLessThan(100);
  });
});
```

## Monitoring Dashboard

#### Performance Metrics Display
```typescript
export const PerformanceMetrics: React.FC = () => {
  const { getMetrics } = usePerformance();
  const metrics = getMetrics();

  return (
    <div className="bg-gray-900 rounded-lg p-3 border border-gray-800">
      <h4 className="text-xs font-medium text-gray-400 mb-2">Performance Metrics</h4>
      <div className="space-y-1 text-xs">
        <div className="flex justify-between">
          <span className="text-gray-500">UI Response:</span>
          <span className={metrics.uiResponseTime > 300 ? 'text-red-400' : 'text-green-400'}>
            {metrics.uiResponseTime.toFixed(1)}ms
          </span>
        </div>
        <div className="flex justify-between">
          <span className="text-gray-500">WASM Processing:</span>
          <span className="text-blue-400">
            {metrics.wasmProcessingTime.toFixed(1)}ms
          </span>
        </div>
        <div className="flex justify-between">
          <span className="text-gray-500">Total Time:</span>
          <span className="text-gray-300">
            {metrics.totalProcessingTime.toFixed(1)}ms
          </span>
        </div>
      </div>
    </div>
  );
};
```

## Risks & Mitigations

**Risk**: Performance monitoring overhead affects performance
**Mitigation**: Use lightweight monitoring and sample-based logging

**Risk**: Skeleton UI doesn't match final component layout
**Mitigation**: Design skeleton components to match final component dimensions

**Risk**: Error boundaries mask important debugging information
**Mitigation**: Implement comprehensive error logging while providing user-friendly messages

## Related Stories

- **Story 1.3**: WebAssembly Build Pipeline Setup (prerequisite)
- **Story 2.1**: Image Upload & Preview (will use these components)
- **Story 2.2**: WebAssembly Image Processing (will use performance monitoring)

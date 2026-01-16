# PickSizer

A modern web application for picking and sizing items with WebAssembly-powered processing and real-time performance monitoring.

## 🚀 Features

- **WebAssembly Integration**: High-performance image processing using Rust + WebAssembly
- **Real-time Performance Monitoring**: Tracks UI response times and processing metrics
- **TypeScript**: Full type safety throughout the application
- **Error Boundaries**: Graceful error handling with accessibility support
- **Supabase Integration**: Backend authentication and data persistence
- **Modern UI**: Built with Next.js, React, and Tailwind CSS

## 🏗️ Architecture

### Tech Stack
- **Frontend**: Next.js 14 + React 19
- **Styling**: Tailwind CSS
- **Language**: TypeScript
- **WebAssembly**: Rust + wasm-pack
- **Backend**: Supabase
- **Performance**: Custom monitoring with <300ms threshold tracking

### Project Structure
```
pick-sizer/
├── src/
│   ├── app/                 # Next.js app router
│   ├── components/           # React components
│   ├── hooks/              # Custom React hooks
│   ├── lib/                # Utility libraries
│   └── types/              # TypeScript definitions
├── wasm/                   # Rust WebAssembly module
├── _bmad/                  # BMAD workflow outputs
└── public/                 # Static assets
```

## 🛠️ Development

### Prerequisites
- Node.js 22.14.0+
- Rust 1.92.0+
- Visual Studio C++ Build Tools (for WebAssembly compilation)

### Setup
```bash
# Install dependencies
pnpm install

# Start development server
pnpm dev

# Run tests
pnpm test

# Build WebAssembly module (requires Rust)
pnpm wasm:build
```

### Environment Variables
```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

## 📊 Performance

The application includes comprehensive performance monitoring:
- **UI Response Time**: Tracks user interaction latency
- **WebAssembly Processing**: Monitors WASM execution time
- **300ms Threshold**: Warns when interactions exceed performance targets
- **Real-time Metrics**: Live performance dashboard

## 🧪 Testing

- **Unit Tests**: Jest + React Testing Library
- **WebAssembly Tests**: Mocked WASM module for reliable testing
- **Performance Tests**: Validates <300ms response requirements
- **Error Boundary Tests**: Ensures graceful failure handling

## 📋 Epic Status

### ✅ Epic 1: Instant Readiness & Performance Foundation (COMPLETE)
- WebAssembly integration with TypeScript types
- Performance monitoring implementation
- Error boundaries and graceful error handling
- Build tools and development environment setup

### 🚧 Epic 2: Core Functionality (NEXT)
- Image processing workflows
- User interface components
- Data persistence layer

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- **Live Demo**: [Coming Soon]
- **Documentation**: [Coming Soon]
- **Issues**: [GitHub Issues](https://github.com/FranklinOuattara/PickSizer/issues)

---

Built with ❤️ using Next.js, Rust, and WebAssembly

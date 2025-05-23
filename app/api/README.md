# CasaLingua API

## Overview

This directory contains the API layer of the CasaLingua application, providing RESTful endpoints for language translation, processing, and management. The API is built using FastAPI and follows a modular, router-based architecture.

## API Optimizations

The API has been optimized for performance, reliability, and scalability with several key features:

### Request-Level Caching

- **Thread-safe** in-memory cache with configurable TTL
- **Bloom Housing compatibility** for seamless integration
- **Metrics tracking** for hit/miss ratios and performance
- **Automatic eviction** for least-recently-used items
- **Configurable size limits** to prevent memory issues

Key file: `app/services/storage/route_cache.py`

### Parallel Processing

- **Concurrent task execution** using asyncio for independent operations
- **Non-blocking I/O** throughout the API stack
- **Parallel preprocessing** for language detection and context retrieval
- **Background tasks** for metrics and logging

Key optimization in: `app/api/routes/pipeline.py`

### Smart Batching

- **Automatic grouping** of similar small requests
- **Adaptive timing control** for optimal batch sizes
- **Result mapping** back to original requesters
- **Integration with caching** for maximum efficiency

Key file: `app/api/middleware/batch_optimizer.py`

### Streaming Responses

- **Chunk-based processing** for large documents
- **Event-driven format** for client-side progress tracking
- **Real-time feedback** during long-running operations
- **Resilience against timeouts** for large translation jobs

Key file: `app/api/routes/streaming.py`

### Enhanced Error Handling

- **Categorized errors** for better client-side handling
- **Standardized format** across all API endpoints
- **Fallback mechanisms** for graceful degradation
- **Request ID tracking** for improved debugging

Key file: `app/utils/error_handler.py`

### Bloom Housing Integration

- **Compatible schemas** that work with both systems
- **Bidirectional conversion** of request/response formats
- **Transparent processing** with CasaLingua's core pipeline

Key file: `app/api/schemas/bloom_housing.py` and `app/api/routes/bloom_housing.py`

## Directory Structure

```
api/
   middleware/         # Request processing middleware
      auth.py         # Authentication handling
      logging.py      # Request logging
      timing.py       # Request timing
      batch_optimizer.py  # Smart request batching
   routes/             # API endpoint definitions
      admin.py        # Admin operations
      health.py       # Health checks
      pipeline.py     # Core processing operations
      rag.py          # RAG endpoints
      streaming.py    # Streaming response endpoints
      bloom_housing.py # Bloom Housing compatibility endpoints
   schemas/            # Data models and validation
       admin.py        # Admin models
       analysis.py     # Analysis models
       base.py         # Base models
       language.py     # Language models
       models.py       # Model management schemas
       queue.py        # Queue status schemas
       bloom_housing.py # Bloom Housing compatibility schemas
       requests/       # Request definitions
           analysis.py
           language.py
           model.py
           requests_*.py  # Various domain-specific requests
```

## Making Requests

### Basic Translation

```http
POST /pipeline/translate
Content-Type: application/json

{
  "text": "Hello world",
  "source_language": "en",
  "target_language": "es",
  "cache": true
}
```

### Streaming Translation

```http
POST /streaming/translate
Content-Type: application/json

{
  "text": "Hello world. This is a long document that will be processed in chunks.",
  "source_language": "en",
  "target_language": "es"
}
```

### Batch Processing

```http
POST /pipeline/batch_translate
Content-Type: application/json

{
  "items": [
    {
      "text": "Hello world", 
      "source_language": "en", 
      "target_language": "es"
    },
    {
      "text": "How are you?", 
      "source_language": "en", 
      "target_language": "es"
    }
  ],
  "smart_batching": true
}
```

## Testing the API

The API optimizations can be tested using the provided test scripts:

```bash
# Test all optimizations
python app/tests/test_optimizations.py

# Test individual components
python app/tests/run_tests.py
```
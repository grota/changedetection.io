# API Integration Analysis - changedetection.io

## Overview

This document analyzes how the REST API integrates with the rest of the changedetection.io codebase, based on examination of the Flask application structure, data layer, and shared services.

## API Architecture

The API is built using **Flask-RESTful** and serves as the primary programmatic interface for managing watches, while sharing core services with the web UI.

### Core Components

- **Flask-RESTful API**: Dedicated `watch_api` instance for REST endpoints
- **Authentication**: Custom token-based system via `x-api-key` header
- **Data Layer**: Shared `ChangeDetectionStore` instance
- **Queue System**: Integration with `RecheckPriorityQueue` for background processing
- **Schema Validation**: Dynamic JSON schema generation from Watch model

## Key Integration Points

### 1. Shared Data Layer

All API resources receive the same `datastore` (ChangeDetectionStore) instance used by the web UI:

```python
# From flask_app.py - API resource registration
watch_api.add_resource(Watch, '/api/v1/watch/<string:uuid>',
                      resource_class_kwargs={'datastore': datastore, 'update_q': update_q})
```

**Integration Benefits:**
- Direct manipulation of watch data, settings, and tags
- API operations trigger the same data persistence mechanisms as web UI actions
- Consistent data model across all interfaces

### 2. Queue System Integration

API endpoints can queue watch checks using the same background processing system:

```python
# From api/Watch.py - triggering watch checks
worker_handler.queue_item_async_safe(self.update_q,
                                   queuedWatchMetaData.PrioritizedItem(priority=1, item={'uuid': uuid}))
```

**Shared Components:**
- `RecheckPriorityQueue` for managing watch check requests
- Async worker system for processing queued items
- Priority-based task scheduling

### 3. Authentication Architecture

```python
# API routes bypass main Flask-Login system
elif request.path.startswith('/api/'):
    return None  # API uses its own auth mechanism
```

**API-Specific Authentication:**
- Token-based authentication via `x-api-key` header
- Configurable through datastore settings (`api_access_token_enabled`)
- `@auth.check_token` decorator for endpoint protection

### 4. Schema Validation System

Request validation using dynamically generated JSON schemas:

```python
# From api/__init__.py - schema generation
watch_base_config = watch_base()
schema = api_schema.build_watch_json_schema(watch_base_config)
```

**Features:**
- Schemas built from Watch model structure
- Separate schemas for create, update, and other operations
- `flask_expects_json` integration for validation

### 5. Processor System Integration

API integrates with the pluggable processor architecture:

```python
# From processors/__init__.py - processor discovery
def get_custom_watch_obj_for_processor(processor_name):
    watch_class = Watch.model
    processor_classes = find_processors()
    # Returns appropriate Watch class based on processor type
```

**Processor Types:**
- Text difference detection
- JSON diff processing
- Restock detection
- Custom processors via plugin system

### 6. Shared Services Architecture

**Content Fetchers:**
- Requests, Playwright, Selenium shared between API and web UI
- Configurable fetch backends (`html_requests`, `html_webdriver`, etc.)
- Proxy support and browser step automation

**Utility Services:**
- HTML processing and diff calculation
- Notification handling system
- Configuration management
- Time handling and scheduling

### 7. Real-time Communication

Both API and web UI integrate with Socket.IO for real-time updates:

```python
# From flask_app.py - shared signal system
app.config['watch_check_update_SIGNAL'] = watch_check_update
```

**Real-time Features:**
- Live status updates for watch checks
- Notification broadcasting
- Queue status monitoring
- Cross-component event signaling

### 8. Blueprint Coordination

Web UI blueprints and API resources operate on the same underlying data:

```python
# From blueprint/watchlist/__init__.py - shared datastore access
def construct_blueprint(datastore: ChangeDetectionStore, update_q, queuedWatchMetaData):
    # Blueprint receives same datastore instance as API
```

**Coordination Benefits:**
- API actions affect web UI display in real-time
- Shared configuration and settings management
- Consistent state across all interfaces

## API Resource Patterns

Each API resource follows a consistent architectural pattern:

### Constructor Pattern
```python
class Watch(Resource):
    def __init__(self, **kwargs):
        self.datastore = kwargs['datastore']  # Shared data access
        self.update_q = kwargs['update_q']    # Queue integration
```

### Authentication Pattern
```python
@auth.check_token
def get(self, uuid):
    # Endpoint implementation
```

### Data Access Pattern
```python
watch = self.datastore.data['watching'].get(uuid)
# Direct datastore manipulation
self.datastore.update_watch(uuid, update_obj)
```

## Data Flow Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Web UI        │    │      API         │    │  Background     │
│  (Blueprints)   │    │  (Flask-RESTful) │    │   Workers       │
└─────────┬───────┘    └────────┬─────────┘    └─────────┬───────┘
          │                     │                      │
          └─────────┬───────────┼──────────┬─────────────┘
                    │           │          │
          ┌─────────▼───────────▼──────────▼─────────────┐
          │              Shared Services                 │
          │                                              │
          │  • ChangeDetectionStore (datastore)          │
          │  • RecheckPriorityQueue (update_q)           │
          │  • Worker Handler                            │
          │  • Content Fetchers                          │
          │  • Notification System                       │
          │  • Processor System                          │
          └──────────────────────────────────────────────┘
```

## Integration Benefits

1. **Consistency**: Single source of truth for all data operations
2. **Real-time Sync**: Changes via API immediately reflected in web UI
3. **Shared Logic**: Common business logic used across all interfaces
4. **Scalability**: Background processing handles both API and UI requests
5. **Maintainability**: Centralized configuration and service management

## Security Considerations

- API token authentication prevents unauthorized access
- CSRF protection on web UI while API remains exempt
- Shared validation logic ensures consistent security policies
- Proxy and header configuration shared securely

## Performance Implications

- Shared queue system prevents resource exhaustion
- Async worker pool handles both API and UI requests
- Caching mechanisms (favicon, screenshot) shared across interfaces
- Database write optimization with `needs_write` flags

This architecture ensures that the API and web UI remain tightly integrated while providing both programmatic and interactive access to the same core functionality, maintaining consistency and real-time synchronization across all user interfaces.</content>
</xai:function_call /></xai:function_call name="run">
<parameter name="command">cd /home/grota/Projects/personal/sabbatical/changedetection.io && ruff check --fix . && ruff format .
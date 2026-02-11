# Evolution API - Project Guide

## What is Evolution API?

Evolution API is a free, open-source REST API that provides programmatic access to WhatsApp messaging. It is built for small businesses, entrepreneurs, and freelancers who need WhatsApp integration without expensive third-party services. The project is maintained at [github.com/EvolutionAPI](https://github.com/EvolutionAPI) and documented at [doc.evolution-api.com](https://doc.evolution-api.com).

## Architecture

- **Runtime**: Node.js application packaged as a Docker image (`atendai/evolution-api`)
- **Default Port**: `8080`
- **API Docs**: Swagger UI at `/docs`
- **Management UI**: Web dashboard at `/manager`
- **Authentication**: API key passed via request header (`AUTHENTICATION_API_KEY`)

## Docker Deployment

### Docker Compose (Recommended)

```yaml
services:
  evolution_api:
    container_name: evolution_api
    image: atendai/evolution-api
    restart: always
    ports:
      - "8080:8080"
    env_file:
      - .env
    volumes:
      - evolution_store:/evolution/store
      - evolution_instances:/evolution/instances

volumes:
  evolution_store:
  evolution_instances:
```

### Required .env

```env
AUTHENTICATION_API_KEY=change-me
```

Additional environment variables are available in the official repository's example `.env` file.

### Common Commands

```bash
docker compose up -d          # Start services
docker logs evolution_api     # View logs
docker compose down           # Stop services
```

### Volumes

| Volume               | Container Path           | Purpose                          |
|----------------------|--------------------------|----------------------------------|
| `evolution_store`    | `/evolution/store`       | Application data persistence     |
| `evolution_instances`| `/evolution/instances`   | WhatsApp instance data           |

Both volumes are critical for surviving container restarts without losing session data.

## Core Concepts

### Instances

An **instance** represents a single WhatsApp connection. Each instance:
- Has its own QR code for linking a WhatsApp account
- Maintains its own session state in `/evolution/instances`
- Can be created, deleted, restarted, and queried via the API
- Exposes connection state (connected/disconnected)

### Messaging Capabilities

The API supports sending:
- Text messages
- Media (images, video, documents)
- Audio messages
- Contacts and locations
- Polls and reactions
- Stickers and status updates
- List messages and template messages

### Chat Operations

- Archive/unarchive chats
- Check WhatsApp number availability
- Delete and update messages
- Fetch profile pictures
- Find chats, contacts, and messages
- Mark messages as read
- Send presence indicators (typing, recording, etc.)

### Group Management

- Create groups and manage members (add/remove/promote/demote)
- Accept invite codes
- Toggle ephemeral messaging
- Update group subject, description, picture, and settings

### Profile Management

- Fetch and update business profiles
- Manage privacy settings
- Update profile name, picture, and status

## Integrations

Evolution API supports the following integrations:

| Integration  | Purpose                                      |
|-------------|----------------------------------------------|
| **Chatwoot** | Open-source customer support platform        |
| **Typebot**  | Chatbot/flow builder                         |
| **RabbitMQ** | Message queue for event-driven architectures |
| **SQS**      | AWS Simple Queue Service integration         |
| **WebSocket**| Real-time bidirectional communication        |
| **Webhooks** | HTTP callbacks for event notifications       |

## Optional Infrastructure

- **MongoDB** - Alternative data store
- **RabbitMQ** - Message broker for async event processing
- **Redis** - Caching and session management
- **WebSocket** - Real-time event streaming

## Development Notes

- Never use `docker run` for production; always use `docker compose`
- The API returns JSON responses
- A Postman collection is available for testing endpoints
- Documentation is available in English and Portuguese

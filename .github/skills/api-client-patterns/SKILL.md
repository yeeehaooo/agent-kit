---
name: api-client-patterns
description: Best practices and patterns for building robust, maintainable API clients with proper error handling, retries, and authentication.
---

# API Client Patterns

## Overview

This skill provides patterns and best practices for building robust API clients that handle errors gracefully, retry failed requests, manage authentication, and provide a clean interface for consuming external APIs.

## When to Use

- When integrating with third-party APIs
- When building internal API clients
- When creating SDK libraries
- When implementing microservice communication

## Core Principles

1. **Reliability**: Handle errors and network issues gracefully
2. **Observability**: Log requests and responses for debugging
3. **Performance**: Cache when appropriate, batch requests
4. **Security**: Protect credentials, validate inputs
5. **Usability**: Provide a clean, intuitive interface

## Basic API Client Structure

### TypeScript/JavaScript

```typescript
// src/clients/api-client.ts
import axios, { AxiosInstance, AxiosRequestConfig } from 'axios';

export interface ApiClientConfig {
  baseURL: string;
  timeout?: number;
  apiKey?: string;
  retries?: number;
  headers?: Record<string, string>;
}

export class ApiClient {
  private client: AxiosInstance;
  private config: ApiClientConfig;

  constructor(config: ApiClientConfig) {
    this.config = {
      timeout: 10000,
      retries: 3,
      ...config
    };

    this.client = axios.create({
      baseURL: config.baseURL,
      timeout: this.config.timeout,
      headers: {
        'Content-Type': 'application/json',
        ...config.headers
      }
    });

    this.setupInterceptors();
  }

  private setupInterceptors() {
    // Request interceptor
    this.client.interceptors.request.use(
      (config) => {
        // Add authentication
        if (this.config.apiKey) {
          config.headers.Authorization = `Bearer ${this.config.apiKey}`;
        }
        
        // Log request
        console.log(`${config.method?.toUpperCase()} ${config.url}`);
        
        return config;
      },
      (error) => {
        return Promise.reject(error);
      }
    );

    // Response interceptor
    this.client.interceptors.response.use(
      (response) => {
        return response;
      },
      async (error) => {
        return this.handleError(error);
      }
    );
  }

  private async handleError(error: any) {
    if (error.response) {
      // Server responded with error status
      throw new ApiError(
        error.response.data.message || 'API request failed',
        error.response.status,
        error.response.data
      );
    } else if (error.request) {
      // Request made but no response
      throw new ApiError('No response from server', 0);
    } else {
      // Request setup error
      throw new ApiError(error.message, 0);
    }
  }

  async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.get<T>(url, config);
    return response.data;
  }

  async post<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.post<T>(url, data, config);
    return response.data;
  }

  async put<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.put<T>(url, data, config);
    return response.data;
  }

  async delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.delete<T>(url, config);
    return response.data;
  }
}

export class ApiError extends Error {
  constructor(
    message: string,
    public statusCode: number,
    public data?: any
  ) {
    super(message);
    this.name = 'ApiError';
  }
}
```

### Python

```python
# src/clients/api_client.py
import requests
from typing import Optional, Dict, Any
from urllib.parse import urljoin
import logging

logger = logging.getLogger(__name__)

class ApiError(Exception):
    """Custom API exception"""
    def __init__(self, message: str, status_code: int = 0, data: Any = None):
        self.message = message
        self.status_code = status_code
        self.data = data
        super().__init__(self.message)

class ApiClient:
    """Base API client with error handling and retries"""
    
    def __init__(
        self,
        base_url: str,
        api_key: Optional[str] = None,
        timeout: int = 10,
        retries: int = 3
    ):
        self.base_url = base_url
        self.api_key = api_key
        self.timeout = timeout
        self.retries = retries
        
        self.session = requests.Session()
        self._setup_session()
    
    def _setup_session(self):
        """Configure session with default headers and auth"""
        self.session.headers.update({
            'Content-Type': 'application/json',
            'User-Agent': 'MyApp/1.0'
        })
        
        if self.api_key:
            self.session.headers['Authorization'] = f'Bearer {self.api_key}'
    
    def _make_request(
        self,
        method: str,
        endpoint: str,
        **kwargs
    ) -> Dict[str, Any]:
        """Make HTTP request with error handling"""
        url = urljoin(self.base_url, endpoint)
        
        try:
            logger.info(f"{method.upper()} {url}")
            response = self.session.request(
                method,
                url,
                timeout=self.timeout,
                **kwargs
            )
            response.raise_for_status()
            return response.json()
            
        except requests.exceptions.HTTPError as e:
            logger.error(f"HTTP error: {e}")
            raise ApiError(
                str(e),
                status_code=e.response.status_code,
                data=e.response.json() if e.response else None
            )
        except requests.exceptions.Timeout:
            logger.error(f"Request timeout: {url}")
            raise ApiError("Request timed out", status_code=0)
        except requests.exceptions.RequestException as e:
            logger.error(f"Request error: {e}")
            raise ApiError(str(e), status_code=0)
    
    def get(self, endpoint: str, params: Optional[Dict] = None) -> Dict[str, Any]:
        """GET request"""
        return self._make_request('GET', endpoint, params=params)
    
    def post(self, endpoint: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        """POST request"""
        return self._make_request('POST', endpoint, json=data)
    
    def put(self, endpoint: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        """PUT request"""
        return self._make_request('PUT', endpoint, json=data)
    
    def delete(self, endpoint: str) -> Dict[str, Any]:
        """DELETE request"""
        return self._make_request('DELETE', endpoint)
```

## Error Handling Patterns

### Retry Logic with Exponential Backoff

```typescript
import axios, { AxiosError } from 'axios';

interface RetryConfig {
  maxRetries: number;
  baseDelay: number;
  maxDelay: number;
}

async function withRetry<T>(
  fn: () => Promise<T>,
  config: RetryConfig = { maxRetries: 3, baseDelay: 1000, maxDelay: 10000 }
): Promise<T> {
  let lastError: Error;

  for (let attempt = 0; attempt <= config.maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error as Error;

      // Don't retry on client errors (4xx)
      if (axios.isAxiosError(error) && error.response?.status) {
        const status = error.response.status;
        if (status >= 400 && status < 500 && status !== 429) {
          throw error;
        }
      }

      if (attempt < config.maxRetries) {
        const delay = Math.min(
          config.baseDelay * Math.pow(2, attempt),
          config.maxDelay
        );
        console.log(`Retry attempt ${attempt + 1} after ${delay}ms`);
        await sleep(delay);
      }
    }
  }

  throw lastError!;
}

function sleep(ms: number): Promise<void> {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Usage
const data = await withRetry(() => apiClient.get('/users'));
```

### Circuit Breaker Pattern

```typescript
class CircuitBreaker {
  private failures = 0;
  private lastFailureTime?: number;
  private state: 'CLOSED' | 'OPEN' | 'HALF_OPEN' = 'CLOSED';

  constructor(
    private threshold: number = 5,
    private timeout: number = 60000
  ) {}

  async execute<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === 'OPEN') {
      if (Date.now() - this.lastFailureTime! < this.timeout) {
        throw new Error('Circuit breaker is OPEN');
      }
      this.state = 'HALF_OPEN';
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  private onSuccess() {
    this.failures = 0;
    this.state = 'CLOSED';
  }

  private onFailure() {
    this.failures++;
    this.lastFailureTime = Date.now();

    if (this.failures >= this.threshold) {
      this.state = 'OPEN';
    }
  }
}

// Usage
const breaker = new CircuitBreaker();
const data = await breaker.execute(() => apiClient.get('/users'));
```

## Authentication Patterns

### JWT Token Management

```typescript
class AuthenticatedApiClient extends ApiClient {
  private accessToken?: string;
  private refreshToken?: string;
  private tokenExpiry?: number;

  async authenticate(username: string, password: string) {
    const response = await this.post<{
      accessToken: string;
      refreshToken: string;
      expiresIn: number;
    }>('/auth/login', { username, password });

    this.accessToken = response.accessToken;
    this.refreshToken = response.refreshToken;
    this.tokenExpiry = Date.now() + response.expiresIn * 1000;
  }

  private async refreshAccessToken() {
    if (!this.refreshToken) {
      throw new Error('No refresh token available');
    }

    const response = await this.post<{
      accessToken: string;
      expiresIn: number;
    }>('/auth/refresh', { refreshToken: this.refreshToken });

    this.accessToken = response.accessToken;
    this.tokenExpiry = Date.now() + response.expiresIn * 1000;
  }

  private async ensureValidToken() {
    if (!this.accessToken || Date.now() >= this.tokenExpiry!) {
      await this.refreshAccessToken();
    }
  }

  async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    await this.ensureValidToken();
    return super.get(url, {
      ...config,
      headers: {
        ...config?.headers,
        Authorization: `Bearer ${this.accessToken}`
      }
    });
  }
}
```

### API Key Management

```typescript
class ApiKeyClient extends ApiClient {
  constructor(apiKey: string, options?: Partial<ApiClientConfig>) {
    super({
      ...options,
      baseURL: options?.baseURL || '',
      headers: {
        'X-API-Key': apiKey,
        ...options?.headers
      }
    });
  }
}
```

## Request/Response Patterns

### Request Validation

```typescript
import { z } from 'zod';

// Define schemas
const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2).max(50),
  age: z.number().min(0).max(150).optional()
});

type CreateUserRequest = z.infer<typeof CreateUserSchema>;

class UsersApiClient {
  constructor(private client: ApiClient) {}

  async createUser(data: CreateUserRequest) {
    // Validate before sending
    const validated = CreateUserSchema.parse(data);
    return this.client.post<User>('/users', validated);
  }
}
```

### Response Transformation

```typescript
interface ApiUser {
  id: string;
  email_address: string;
  full_name: string;
  created_timestamp: string;
}

interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
}

class UsersApiClient {
  async getUser(id: string): Promise<User> {
    const apiUser = await this.client.get<ApiUser>(`/users/${id}`);
    return this.transformUser(apiUser);
  }

  private transformUser(apiUser: ApiUser): User {
    return {
      id: apiUser.id,
      email: apiUser.email_address,
      name: apiUser.full_name,
      createdAt: new Date(apiUser.created_timestamp)
    };
  }
}
```

## Pagination Patterns

### Cursor-based Pagination

```typescript
interface PaginatedResponse<T> {
  data: T[];
  cursor?: string;
  hasMore: boolean;
}

class PaginatedApiClient {
  async *getAllUsers(): AsyncGenerator<User> {
    let cursor: string | undefined;

    do {
      const response = await this.client.get<PaginatedResponse<User>>(
        '/users',
        { params: { cursor, limit: 100 } }
      );

      for (const user of response.data) {
        yield user;
      }

      cursor = response.cursor;
    } while (cursor);
  }
}

// Usage
for await (const user of apiClient.getAllUsers()) {
  console.log(user);
}
```

### Page-based Pagination

```typescript
interface PagedResponse<T> {
  items: T[];
  page: number;
  totalPages: number;
  total: number;
}

class PagedApiClient {
  async getUsers(page: number = 1, perPage: number = 20): Promise<PagedResponse<User>> {
    return this.client.get<PagedResponse<User>>('/users', {
      params: { page, perPage }
    });
  }

  async *getAllUsersPages(): AsyncGenerator<User[]> {
    let page = 1;
    let totalPages: number;

    do {
      const response = await this.getUsers(page);
      yield response.items;
      
      totalPages = response.totalPages;
      page++;
    } while (page <= totalPages);
  }
}
```

## Caching Patterns

### In-Memory Cache

```typescript
interface CacheEntry<T> {
  data: T;
  expiry: number;
}

class CachedApiClient extends ApiClient {
  private cache = new Map<string, CacheEntry<any>>();

  async getCached<T>(
    url: string,
    ttl: number = 60000 // 1 minute
  ): Promise<T> {
    const cached = this.cache.get(url);

    if (cached && Date.now() < cached.expiry) {
      return cached.data;
    }

    const data = await this.get<T>(url);
    this.cache.set(url, {
      data,
      expiry: Date.now() + ttl
    });

    return data;
  }

  invalidateCache(url?: string) {
    if (url) {
      this.cache.delete(url);
    } else {
      this.cache.clear();
    }
  }
}
```

## Rate Limiting

### Client-side Rate Limiter

```typescript
class RateLimiter {
  private queue: Array<() => void> = [];
  private tokens: number;

  constructor(
    private maxTokens: number,
    private refillRate: number // tokens per second
  ) {
    this.tokens = maxTokens;
    this.startRefilling();
  }

  private startRefilling() {
    setInterval(() => {
      this.tokens = Math.min(this.tokens + 1, this.maxTokens);
      this.processQueue();
    }, 1000 / this.refillRate);
  }

  private processQueue() {
    while (this.tokens > 0 && this.queue.length > 0) {
      const next = this.queue.shift();
      this.tokens--;
      next?.();
    }
  }

  async acquire(): Promise<void> {
    if (this.tokens > 0) {
      this.tokens--;
      return Promise.resolve();
    }

    return new Promise<void>(resolve => {
      this.queue.push(resolve);
    });
  }
}

class RateLimitedApiClient extends ApiClient {
  private limiter = new RateLimiter(10, 1); // 10 requests per second

  async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    await this.limiter.acquire();
    return super.get(url, config);
  }
}
```

## Batch Requests

```typescript
class BatchApiClient {
  private batchQueue: Array<{ id: string; resolve: Function; reject: Function }> = [];
  private batchTimer?: NodeJS.Timeout;

  async getUserById(id: string): Promise<User> {
    return new Promise((resolve, reject) => {
      this.batchQueue.push({ id, resolve, reject });

      if (!this.batchTimer) {
        this.batchTimer = setTimeout(() => this.processBatch(), 50);
      }
    });
  }

  private async processBatch() {
    const batch = this.batchQueue.splice(0);
    this.batchTimer = undefined;

    try {
      const ids = batch.map(item => item.id);
      const users = await this.client.post<User[]>('/users/batch', { ids });

      const userMap = new Map(users.map(u => [u.id, u]));

      batch.forEach(item => {
        const user = userMap.get(item.id);
        if (user) {
          item.resolve(user);
        } else {
          item.reject(new Error(`User ${item.id} not found`));
        }
      });
    } catch (error) {
      batch.forEach(item => item.reject(error));
    }
  }
}
```

## Testing API Clients

### Mocking with MSW

```typescript
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('https://api.example.com/users', (req, res, ctx) => {
    return res(
      ctx.json([
        { id: '1', name: 'Alice' },
        { id: '2', name: 'Bob' }
      ])
    );
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('fetches users', async () => {
  const client = new ApiClient({ baseURL: 'https://api.example.com' });
  const users = await client.get<User[]>('/users');
  
  expect(users).toHaveLength(2);
  expect(users[0].name).toBe('Alice');
});
```

## Best Practices

### DO:
✓ Use TypeScript for type safety
✓ Implement proper error handling
✓ Add retry logic for transient failures
✓ Log requests and responses
✓ Validate input before sending
✓ Transform responses to domain models
✓ Cache when appropriate
✓ Respect rate limits
✓ Use environment variables for configuration
✓ Implement timeouts
✓ Handle authentication token refresh

### DON'T:
✗ Hardcode API keys or secrets
✗ Swallow errors silently
✗ Retry on client errors (4xx)
✗ Block the main thread
✗ Make unnecessary requests
✗ Expose implementation details
✗ Ignore security headers
✗ Skip input validation
✗ Log sensitive data

## Security Checklist

- [ ] API keys stored in environment variables
- [ ] HTTPS used for all requests
- [ ] Input sanitized and validated
- [ ] Sensitive data not logged
- [ ] Authentication tokens handled securely
- [ ] CSRF protection implemented
- [ ] Rate limiting in place
- [ ] Timeout configured
- [ ] Error messages don't leak sensitive info

## Resources

- [Axios Documentation](https://axios-http.com/)
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [Requests Library (Python)](https://requests.readthedocs.io/)
- [API Design Best Practices](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/)
- [HTTP Status Codes](https://httpstatuses.com/)

# Typed API Client Architecture

Axios interceptors, automatic JWT injection, and 401 refresh token retry loops.

---

## 1. Axios Client Configuration

```typescript
import axios from 'axios';
import { useAuthStore } from '../stores/authStore';

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_GATEWAY_URL || '/api/v1',
  withCredentials: true, // Send HttpOnly refresh token cookie
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request Interceptor: Inject JWT Bearer Token
apiClient.interceptors.request.use((config) => {
  const token = useAuthStore.getState().accessToken;
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response Interceptor: 401 Token Refresh Queue
let isRefreshing = false;
let failedQueue: Array<{ resolve: (token: string) => void; reject: (err: any) => void }> = [];

apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;
    if (error.response?.status === 401 && !originalRequest._retry) {
      if (isRefreshing) {
        return new Promise((resolve, reject) => {
          failedQueue.push({ resolve, reject });
        }).then((token) => {
          originalRequest.headers.Authorization = `Bearer ${token}`;
          return apiClient(originalRequest);
        });
      }

      originalRequest._retry = true;
      isRefreshing = true;

      try {
        const { data } = await axios.post('/api/v1/auth/refresh', {}, { withCredentials: true });
        useAuthStore.getState().setAccessToken(data.accessToken);
        failedQueue.forEach((prom) => prom.resolve(data.accessToken));
        failedQueue = [];
        return apiClient(originalRequest);
      } catch (refreshError) {
        useAuthStore.getState().logout();
        failedQueue.forEach((prom) => prom.reject(refreshError));
        failedQueue = [];
        return Promise.reject(refreshError);
      } finally {
        isRefreshing = false;
      }
    }
    return Promise.reject(error);
  }
);
```

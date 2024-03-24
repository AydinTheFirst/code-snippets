```js
import axios from "axios";
import { toast } from "sonner";

export const BASE = import.meta.env.VITE_API || "http://localhost:3000";
export const API: string = BASE + "/api";
export const CDN: string = BASE + "/cdn";

export const http = axios.create({
  baseURL: API,
  timeout: 10000,
  headers: {
    Authorization: localStorage.getItem("token") || "Bearer token",
  },
});

export const fetcher = (url: string) => http.get(url).then((res) => res.data);

export const httpError = (error: any) => {
  if (error.response.status === 401) {
    location.href = "/login";
    localStorage.removeItem("token");
  }

  let errorMessage = "Something went wrong!";
  let errorDescription = error.message;

  if (error.response) {
    const { message, errors } = error.response.data;
    if (message) errorMessage = message;
    if (errors) errorDescription = zodError(errors);
  } else if (error.request) {
    errorMessage = error.request;
  } else {
    errorMessage = error.message;
  }

  toast.error(errorMessage, {
    description: errorDescription,
    descriptionClassName: "whitespace-pre-wrap",
  });
};

const zodError = (error: any) => {
  const json = error.issues.map((err: any) => {
    return {
      message: err.message,
      field: err.path.join("."),
    };
  });

  return JSON.stringify(json, null, 2);
};
```

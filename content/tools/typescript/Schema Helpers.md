For use with openapi yml generation to easily pull the response and request types

```ts
import { paths } from '../schema';

/**
 * Grab the body application/json from a requestBody post request
 * */
export type PostBody<T> = T extends {
  post: {
    requestBody?: {
      content: {
        'application/json': infer C;
      };
    };
  };
}
  ? C
  : never;
/**
 * Get the response of a post request
 * */
export type PostResponse<T> = T extends { post: { responses: {['200']:{content:{ 'application/json': infer C}}}}} ? C : never


/**
 * Grab params of a get request
 * */
export type GetParams<T> = T extends { get: { parameters: { query: infer C}}} ? C : never


/**
 * Get the response of a get request
 * */
export type GetResponse<T> = T extends { get: { responses: {['200']:{content:{ 'application/json': infer C}}}}} ? C : never


```
# Ministry of Programming Async/Await Style Guide

*Instructions and tips how to use Async/Await*

## Table of Contents

  1. [Basic Rules](#basic-rules)

## Basic Rules

 - Always use ES7 **[Async / Await](https://tc39.github.io/ecmascript-asyncawait/)** to write asynchronous control flow in code. Do not use another methods to write asynchronous code, **Promises, Generators / Yields (ES6)**
 - Always use **[bluebird](https://github.com/petkaantonov/bluebird/)** as a promise library

## Example 1 - Make simple GET request using [request](https://github.com/request/request) module

 - Solution using `callbacks`
     ```javascript
     function getRequest(url, callback) {
       request(url, (error, response, body) => {
         if (error || response.statusCode !== 200) {
           callback(error || new Error(response.statusCode));
         } else {
           callback(null, body);
         }
       });
     }

     getRequest('https://httpbin.org/ip', (error, body) => {
       if (error) {
         console.error(error);
       } else {
         console.log(body);
       }
     });
     ```
 - Solution using `async/await`
    ```javascript
    function getRequest(url) {
      return new Promise((resolve, reject) => {
        request(url, (error, response, body) => {
          if (error || response.statusCode !== 200) {
            reject(error || new Error(response.statusCode));
          } else {
            resolve(body);
          }
        });
      });
    }

    async function test() {
      try {
        const body = await getRequest('https://httpbin.org/ip');
        console.log(body);
      } catch (err) {
        console.error(err);
      }
    }

    test();
    ```

## Example 2 - Make multiple GET requests using [request](https://github.com/request/request) module

*Get list of `posts` from RESTful API and get `comments` for first post*

 - Solution using `callbacks`
     ```javascript
     function getRequest(url, callback) {
       request(url, (error, response, body) => {
         if (error || response.statusCode !== 200) {
           callback(error || new Error(response.statusCode));
         } else {
           callback(null, body);
         }
       });
     }

     const apiUrl = 'http://jsonplaceholder.typicode.com';
     getRequest(`${apiUrl}/posts`, (errPosts, bodyPosts) => {
       if (errPosts) {
         console.error(errPosts);
       } else {
         const firstPost = JSON.parse(bodyPosts)[0];
         console.log(`Post title: ${firstPost.title}`);

         getRequest(`${apiUrl}/posts/${firstPost.id}/comments`, (errComments, bodyComments) => {
           if (errComments) {
             console.error(errComments);
           } else {
             const comments = JSON.parse(bodyComments);
             console.log(`Comments count: ${comments.length}`);
           }
         });
       }
     });
     ```
 - Solution using `async/await`
    ```javascript
    function getRequest(url) {
      return new Promise((resolve, reject) => {
        request(url, (error, response, body) => {
          if (error || response.statusCode !== 200) {
            reject(error || new Error(response.statusCode));
          } else {
            resolve(body);
          }
        });
      });
    }

    async function test() {
      const apiUrl = 'http://jsonplaceholder.typicode.com';
      try {
        const bodyPosts = await getRequest(`${apiUrl}/posts`);
        const firstPost = JSON.parse(bodyPosts)[0];
        console.log(`Post title: ${firstPost.title}`);

        const bodyComments = await getRequest(`${apiUrl}/posts/${firstPost.id}/comments`);
        const comments = JSON.parse(bodyComments);
        console.log(`Comments count: ${comments.length}`);
      } catch (err) {
        console.error(err);
      }
    }

    test();
    ```
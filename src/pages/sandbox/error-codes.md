# Error codes

- 2xx – Success
- 4xx – Error in client’s request
- 5xx – Error in Adobe servers

| Status code | Message               | Description                                                                                                                           |
|:------------|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| 200         | OK                    | Everything worked as expected                                                                                                         |
| 201         | Created               | Resource has been successfully created                                                                                                |
| 202         | Accepted              | Request has been accepted and will be acted on later                                                                                  |
| 400         | Bad Request           | The request was unable to be completed due to some client error                                                                       |
| 401         | Unauthorized          | The authorization token is invalid                                                                                                    |
| 403         | Forbidden             | The API key is invalid                                                                                                                |
| 404         | Not Found             | The requested resource does not exist                                                                                                 |
| 429         | Too Many Requests     | The client has sent too many requests in a given period. |
| 500         | Internal Server Error | There was an error on Adobe’s end. Please try the request again                                                                           |

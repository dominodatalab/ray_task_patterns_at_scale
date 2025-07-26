# Managing large number of ray tasks

Ray has several hard limits:

- `max_in_flight_requests`: limits how many pending submissions the client can buffer.

- `max_calls_in_flight_per_worker`: limits how many calls per worker can be open.

- Network gRPC queue sizes.

These limits exist to protect the system from task flood.




| Config Param                      | Default (Est.) | Scope      | How to Change                     | Inspectable |
|-----------------------------------|----------------|------------|-----------------------------------|-------------|
| `max_in_flight_requests`          | ~1000          | Ray Client | RAY_CLIENT_MAX_PENDING_REQUESTS   | No          |
| `max_calls_in_flight_per_worker`  | ~1000          | Worker     | Not exposed                       | No          |
| `gRPC queue limits`               | Internal       | All nodes  | Not configurable via user API     | No          |


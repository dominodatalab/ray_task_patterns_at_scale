# Managing large number of ray tasks


1. [Open ray_large_number_of_workers_in_queue.ipynb](ray_large_number_of_workers_in_queue.ipynb)

Follow this notebook when you have a situation where you need to submit an arbitrarily high number of ray worker tasks

-----

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

2. [Open ray_actor_vs_function.ipynb](ray_actor_vs_function.ipynb)

In general avoid actors in favor of functions if:

- The number of tasks is very high (tens of thousands or more)
- Tasks are stateless and do not require persistent context
- You do not need method-level serialization or concurrency guarantees
- You want to maximize resource elasticity and throughput

| Reason                  | Explanation                                                                     |
|-------------------------|---------------------------------------------------------------------------------|
| Fixed resource pinning  | Actors hold CPU/GPU for their lifetime, even when idle                         |
| Scheduling bottlenecks  | Actor methods must be routed to specific workers hosting those actors           |
| Queuing pressure        | Each actor has its own queue; Raylet must manage and serialize them             |
| Higher control-plane load | Actor lifecycle, method routing, and failure handling are more expensive     |
| Less resilience         | Actor crashes interrupt dependent tasks; recovery is harder                     |

Use tasks instead when:

- You can express work as pure functions
- You want fine-grained, massively parallel execution
- You need dynamic scaling and better memory/resource churn


Use actors when:

- You need to hold state between calls
- You're managing a service, connection, or cache
- You need in-process coordination, streaming, or long-lived control loops

**When there is high task count, prefer functions**. Actors scale poorly under high submission pressure unless strictly necessary and carefully managed.

# Design Patterns
- https://learn.microsoft.com/en-us/azure/architecture/patterns/
## Circuit Breaker
- a way to protect a system from failures or excessive load by temporarily stopping certain operations if the system is deemed to be in a failed or overloaded state
- used in cloud computing environments to prevent cascading failures and to improve the resilience and availability of a system
- consists of three states: closed, open, and half-open
- closed state: operations proceed as normal
- open state: when system encounters a failure or becomes overloaded
	- all subsequent operations are stopped
	- after a period of time, circuit breaker moves to half-open state
 - half-open state:
	 - allow a small number of operations
	 - if sucessful -> closed, else -> open
## Throttling
- limit the max number of requests  that can be made to a system or component within a time period
- or limit resources used
## Graceful Degradation
- when demand exceeds capacity and risks damaging the system, you may need to:
	- Shed load
		- drop low priority requests
	- Time shift/delay
		- Use message queues to add a time buffer
	- Add capacity via auto-scaling
	- Reduce quality of service
- when data production exceeds rate of data consumption
	- Buffering
		- store incoming data in a buffer until it can be processed
	- Batching
		- group incoming data into batches and process batches in sequence, rather than individually
# Pipeline Parallelism
- model split layer wise across GPUs
- naive implementation -> poor GPU utilization as a GPU is idle until its needed
	- each GPU would be busy only 1/(#GPUs) % of the time
	- need to assign useful work to GPUs that are idling
## GPipe
- splitting batches to micro batches can improve utilization as GPU can move onto next microbatch
- |Timestep|0|1|2|3|4|5|6|7|8|9|10|11|12|13|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|GPU3||||F1|F2|F3|F4|B4|B3|B2|B1||||
|GPU2|||F1|F2|F3|F4|||B4|B3|B2|B1|||
|GPU1||F1|F2|F3|F4|||||B4|B3|B2|B1||
|GPU0|F1|F2|F3|F4|||||||B4|B3|B2|B1|
- instead of
- |Timestep|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|
|GPU3||||FWD|BWD||||
|GPU2|||FWD|||BWD|||
|GPU1||FWD|||||BWD||
|GPU0|FWD|||||||BWD|
- improves gpu utilization / reduces pipeline bubbles
- used activation recomputation to reduce memory
## PipeDream
- starts the backward pass for a microbatch as soon as the final pipeline stage has completed the corresponding forward pass
- discard the cached activation for the m’th microbatch as soon as we perform the corresponding backward pass
	- happens earlier than in GPipe, which reduces the memory demand
- performed by interweaving forwards and backwards to reduce the number of microbatches in flight
	- in-flight: if forward pass is performed but backward pass isn't complete
	- 1F1B schedule: during steady state (after warm-up phase), forward and backwards of a microbatch occurs one after another
- many different interweaving schedules
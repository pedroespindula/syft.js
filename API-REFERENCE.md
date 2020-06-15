<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

- [Syft][1]
  - [Parameters][2]
  - [Examples][3]
  - [newJob][4]
    - [Parameters][5]
- [Job][6]
  - [Properties][7]
  - [on][8]
    - [Parameters][9]
  - [start][10]
  - [report][11]
    - [Parameters][12]
- [Job#accepted][13]
  - [Properties][14]
- [Job#rejected][15]
  - [Properties][16]
- [Job#error][17]
- [SyftModel][18]
  - [Properties][19]
  - [createSerializedDiff][20]
    - [Parameters][21]
- [Plan][22]
  - [execute][23]
    - [Parameters][24]

## Syft

Syft client for static federated learning.

### Parameters

- `options` **[Object][25]**
  - `options.url` **[string][26]** Full URL to PyGrid app (`ws` and `http` schemas supported).
  - `options.verbose` **[boolean][27]** Whether to enable logging and allow unsecured PyGrid connection.
  - `options.authToken` **[string][26]** PyGrid authentication token.
  - `options.peerConfig` **[Object][25]** [not implemented] WebRTC peer config used with RTCPeerConnection.

### Examples

```javascript
const client = new Syft({url: "ws://localhost:5000", verbose: true})
const job = client.newJob({modelName: "mnist", modelVersion: "1.0.0"})
job.on('accepted', async (model, config) => {
  // execute training
  const [...newParams] = await this.plans['...'].execute(...)
  const diff = await model.createSerializedDiff(newParams)
  await this.report(diff)
})
job.on('rejected', (timeout) => {
  // re-try later or stop
})
job.on('error', (err) => {
  // handle errors
})
job.start()
```

### newJob

Authenticates the client against PyGrid and instantiates new Job with given options.

#### Parameters

- `options` **[Object][25]**
  - `options.modelName` **[string][26]** FL Model name.
  - `options.modelVersion` **[string][26]** FL Model version.

Returns **[Promise][28]&lt;[Job][29]>**

## Job

Job represents a single training cycle done by the client.

### Properties

- `plans` **[Object][25]&lt;[string][26], [Plan][30]>** Plans dictionary.
- `protocols` **[Object][25]&lt;[string][26], Protocol>** [not implemented] Protocols dictionary.
- `model` **[SyftModel][31]** Model.

### on

Registers an event listener.

Available events: `accepted`, `rejected`, `error`.

#### Parameters

- `event` **[string][26]** Event name.
- `handler` **[function][32]** Event handler.

### start

Starts the Job executing following actions:

- Meters connection speed to PyGrid.
- Registers into training cycle on PyGrid.
- Retrieves cycle and client parameters.
- Downloads Plans, Model, Protocols.
- Fires `accepted` event on success.

Returns **[Promise][28]&lt;void>**

### report

Submits the model diff to PyGrid.

#### Parameters

- `diff` **[ArrayBuffer][33]** Serialized difference between original and trained model parameters.

Returns **[Promise][28]&lt;void>**

## Job#accepted

`accepted` event.
Triggered PyGrid accepts the client into training cycle.

Type: [Object][25]

### Properties

- `model` **[SyftModel][31]** Instance of SyftModel.
- `clientConfig` **[Object][25]** Client configuration returned by PyGrid.

## Job#rejected

`rejected` event.
Triggered when PyGrid rejects the client.

Type: [Object][25]

### Properties

- `timeout` **([number][34] | null)** Time in seconds to re-try. Empty when the FL model is not trainable anymore.

## Job#error

`error` event.
Triggered for plethora of error conditions.

## SyftModel

Model parameters as stored in the PyGrid.

### Properties

- `params` **[Array][35]&lt;tf.Tensor>** Array of Model parameters.

### createSerializedDiff

Calculates difference between 2 versions of the Model parameters
and returns serialized `diff` that can be submitted to PyGrid.

#### Parameters

- `updatedModelParams` **[Array][35]&lt;tf.Tensor>** Array of model parameters (tensors).

Returns **[Promise][28]&lt;[ArrayBuffer][33]>** Protobuf-serialized `diff`.

## Plan

PySyft Plan.

### execute

Executes the Plan and returns its output.

The order, type and number of arguments must match to arguments defined in the PySyft Plan.

#### Parameters

- `worker` **[Syft][36]**
- `data` **...(tf.Tensor | [number][34])**

Returns **[Promise][28]&lt;[Array][35]&lt;tf.Tensor>>**

[1]: #syft
[2]: #parameters
[3]: #examples
[4]: #newjob
[5]: #parameters-1
[6]: #job
[7]: #properties
[8]: #on
[9]: #parameters-2
[10]: #start
[11]: #report
[12]: #parameters-3
[13]: #jobaccepted
[14]: #properties-1
[15]: #jobrejected
[16]: #properties-2
[17]: #joberror
[18]: #syftmodel
[19]: #properties-3
[20]: #createserializeddiff
[21]: #parameters-4
[22]: #plan
[23]: #execute
[24]: #parameters-5
[25]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object
[26]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String
[27]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean
[28]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise
[29]: #job
[30]: #plan
[31]: #syftmodel
[32]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function
[33]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer
[34]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number
[35]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array
[36]: #syft
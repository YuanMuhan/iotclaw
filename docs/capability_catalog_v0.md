# Capability Catalog v0

## 1. Purpose

`Capability Catalog` defines the **primitive semantic capabilities** that the D1 decomposition stage is allowed to use.

Its role is to provide a **controlled capability vocabulary** between:

- normalized natural language task specifications
- D1 graph generation
- later skill composition and verification

This catalog is **not**:

- a device inventory
- a protocol list
- a concrete tool library
- a code function registry

Instead, it defines **abstract functional primitives** such as `detect`, `compare`, and `notify`.

---

## 2. Design principles

1. Keep the primitive set small in v0.
2. Use semantic capability primitives, not implementation-level tools.
3. Separate capability from device instance, protocol, and deployment.
4. Make the catalog stable enough for D1 graph generation and verification.
5. Reserve more detailed tool/runtime binding for later stages (D2/D3).

---

## 3. What capability means

A capability answers the question:

**“What kind of function is needed here?”**

Examples:

- `detect`
- `compare`
- `act`
- `notify`

A capability does **not** answer:

- which model to call
- which API to invoke
- which MQTT topic to publish to
- which Python function to import

Those belong to later implementation layers.

---

## 4. Catalog scope in v0

This v0 catalog includes only **primitive semantic capabilities**.

It does not yet include:

- composite skills
- protocol-level operations
- concrete tools
- device-specific actions
- communication bindings

---

## 5. Capability families

To keep the catalog interpretable, the primitive capabilities are grouped into families.

### 5.1 Acquisition
Capabilities for obtaining input data.

- `sense`
- `read`

### 5.2 Processing
Capabilities for transforming, analyzing, or evaluating inputs.

- `filter`
- `detect`
- `infer`
- `compare`

### 5.3 Decision and Execution
Capabilities for selecting responses and executing actions.

- `decide`
- `act`

### 5.4 Interaction and Persistence
Capabilities for communicating results or storing information.

- `notify`
- `store`
- `schedule`

---

## 6. Primitive capability list (v0)

The following 11 primitive capabilities are included in v0:

- `sense`
- `read`
- `filter`
- `detect`
- `infer`
- `compare`
- `decide`
- `act`
- `notify`
- `store`
- `schedule`

---

## 7. Primitive definitions

### 7.1 `sense`
- Family: `acquisition`
- Meaning: Collect raw data from the physical environment.
- Typical examples:
  - capture video frame from camera
  - sample temperature from sensor
  - sample audio from microphone

#### Notes
Use `sense` when the system acquires data from the physical world.

---

### 7.2 `read`
- Family: `acquisition`
- Meaning: Read existing digital state, records, files, or cached values.
- Typical examples:
  - read device state
  - read historical log
  - read local configuration
  - read cached sensor value

#### Notes
Use `read` when the data already exists in the system.

---

### 7.3 `filter`
- Family: `processing`
- Meaning: Clean, preprocess, prune, or denoise input data.
- Typical examples:
  - crop image
  - smooth sensor values
  - remove noisy samples
  - preprocess text

#### Notes
`filter` improves data quality but does not perform high-level semantic recognition.

---

### 7.4 `detect`
- Family: `processing`
- Meaning: Identify whether a target event, object, or pattern exists.
- Typical examples:
  - detect fall event
  - detect smoke
  - detect motion
  - detect whether a door is open

#### Notes
`detect` is usually used for explicit event/object recognition.

---

### 7.5 `infer`
- Family: `processing`
- Meaning: Produce a higher-level semantic interpretation or latent state estimate.
- Typical examples:
  - infer activity type
  - infer risk level
  - infer health status trend

#### Notes
`infer` is more abstract than `detect`.

---

### 7.6 `compare`
- Family: `processing`
- Meaning: Compare input values against thresholds, ranges, or conditions.
- Typical examples:
  - temperature > threshold
  - risk score exceeds alert line
  - state equals target state

#### Notes
`compare` is typically a local condition evaluation step.

---

### 7.7 `decide`
- Family: `decision_execution`
- Meaning: Choose a strategy, branch, or response based on previous results.
- Typical examples:
  - decide whether to trigger alarm
  - decide whether to escalate
  - decide next action path

#### Notes
`decide` is higher-level than `compare`, and often consumes multiple upstream results.

---

### 7.8 `act`
- Family: `decision_execution`
- Meaning: Execute an action that changes the device, system, or physical environment.
- Typical examples:
  - turn on light
  - unlock door
  - start fan
  - trigger buzzer

#### Notes
`act` changes state; it is different from sending a message.

---

### 7.9 `notify`
- Family: `interaction_persistence`
- Meaning: Send information, alarm, reminder, or report to a person or external system.
- Typical examples:
  - send app notification
  - send SMS
  - send email
  - report to control dashboard

#### Notes
`notify` communicates information; it does not directly change physical state.

---

### 7.10 `store`
- Family: `interaction_persistence`
- Meaning: Persist data, results, or records for later use.
- Typical examples:
  - write database entry
  - append log
  - save inference result
  - archive history

#### Notes
Use `store` only for persistence, not for temporary internal data flow.

---

### 7.11 `schedule`
- Family: `interaction_persistence`
- Meaning: Define periodic or time-based triggering.
- Typical examples:
  - sample every 5 seconds
  - run daily check at 8:00
  - sync hourly

#### Notes
`schedule` expresses time-driven triggering rather than processing logic itself.

---

## 8. Important distinctions

### `sense` vs `read`
- `sense`: obtain raw data from the physical world
- `read`: obtain already available digital data

### `detect` vs `infer`
- `detect`: explicit event/object/pattern recognition
- `infer`: higher-level semantic or latent-state reasoning

### `compare` vs `decide`
- `compare`: local threshold/condition evaluation
- `decide`: higher-level selection among possible actions or branches

### `act` vs `notify`
- `act`: change system or physical state
- `notify`: communicate information to user or external system

---

## 9. What is intentionally excluded in v0

The following are intentionally excluded from the capability catalog:

### 9.1 Concrete tools
Examples:
- `mqtt_publish`
- `http_post`
- `yolo_detect`
- `send_sms_via_twilio`

These are implementation-level concepts, not abstract capabilities.

### 9.2 Device instances
Examples:
- `camera_1`
- `living_room_light`
- `sensor_2`

These belong to device information, not the catalog.

### 9.3 Protocol or transport details
Examples:
- MQTT
- HTTP
- CoAP
- WebSocket

These belong to later communication/binding design.

### 9.4 `aggregate`
This is intentionally omitted in v0 to keep the primitive set small and reduce ambiguity.
It may be added in future versions when multi-source fusion or time-window aggregation becomes necessary.

### 9.5 `transmit`
This is intentionally omitted in v0.
Graph edges already represent dependency/data flow, and actual communication design belongs to later stages.

---

## 10. Minimal recommended fields for each capability profile

Each capability in the catalog should at least include:

- `name`
- `family`
- `description`
- `input_types`
- `output_types`
- `examples`

These are enough for v0 grounding and validation.

---

## 11. Example usage

A D1 node may refer to a capability from this catalog like this:

```json
{
  "id": "n2",
  "label": "detect_fall_event",
  "capability": "detect",
  "inputs": ["video_frame"],
  "outputs": ["fall_event"]
}
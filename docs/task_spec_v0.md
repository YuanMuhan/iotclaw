# Normalized Task Spec v0

## 1. Purpose

`Normalized Task Spec` is the first structured representation after receiving a user's natural language request.

Its goal is to convert raw, possibly colloquial or ambiguous natural language into a concise, machine-readable task specification that can be consumed by the D1 decomposition pipeline.

This layer is **not** the final decomposition result, and it is **not** code-level input.  
It only clarifies:

- what the task is trying to achieve
- what inputs are involved
- what outputs are expected
- what extra remarks or assumptions should be retained
- what high-level non-functional constraints may affect later D2/D3 stages

---

## 2. Design principles

1. Keep this layer simple and stable.
2. Do not include implementation details.
3. Do not include deployment decisions.
4. Do not include protocol-level details such as MQTT topics, URLs, ports, API endpoints, etc.
5. Use this layer only to normalize task semantics before capability grounding and graph generation.

---

## 3. Schema overview

A `Normalized Task Spec` contains the following top-level fields:

- `task_id` (optional)
- `target` (required)
- `remarks` (optional)
- `inputs` (required)
- `outputs` (required)
- `constraints` (optional)

---

## 4. Field definitions

### 4.1 `task_id`
- Type: `string`
- Required: No
- Meaning: Optional identifier for the task instance.
- Example:
  - `"fall_alarm_case_001"`

---

### 4.2 `target`
- Type: `string`
- Required: Yes
- Meaning: A concise statement of the final task goal.
- Should contain:
  - the overall task objective
- Should NOT contain:
  - step-by-step decomposition
  - code details
  - protocol details
- Good example:
  - `"Detect a fall event and send an alarm notification."`
- Bad example:
  - `"Read camera frames, detect falls, then send MQTT messages."`

---

### 4.3 `remarks`
- Type: `array[string]`
- Required: No
- Meaning: Additional context, assumptions, user notes, or scenario descriptions that do not fit cleanly into `inputs`, `outputs`, or `constraints`.
- Should contain:
  - scenario assumptions
  - user-specified notes
  - environment hints
  - data format hints
- Example:
  - `"The system is deployed in a home environment."`
  - `"Only abnormal events need to trigger notifications."`

---

### 4.4 `inputs`
- Type: `array[object]`
- Required: Yes
- Meaning: Task-level inputs required to complete the task.
- Important:
  - These are **task inputs**, not code function arguments.
  - They describe semantic inputs available to the system.
- Each input object contains:
  - `name`: short input name
  - `type`: abstract type name
  - `description`: semantic description
  - `source`: one of `user | device | system | external`
- Good examples:
  - camera stream
  - temperature reading
  - threshold value
  - user text request
- Bad examples:
  - `broker_url`
  - `/api/v1/notify`
  - `mqtt_topic`

#### Input object fields

##### `name`
- Type: `string`
- Required: Yes
- Meaning: Short input identifier.

##### `type`
- Type: `string`
- Required: Yes
- Meaning: Abstract semantic type, not programming language type.
- Examples:
  - `ImageFrame`
  - `Temperature`
  - `Boolean`
  - `Text`

##### `description`
- Type: `string`
- Required: Yes
- Meaning: Human-readable explanation of the input.

##### `source`
- Type: `string`
- Required: No
- Allowed values:
  - `user`
  - `device`
  - `system`
  - `external`
- Meaning: Where this input comes from.

---

### 4.5 `outputs`
- Type: `array[object]`
- Required: Yes
- Meaning: Task-level outputs expected from the system.
- Important:
  - These are **expected results**, not implementation-level return variables.
- Good examples:
  - alarm notification
  - detected activity label
  - stored log record
  - control action

#### Output object fields

##### `name`
- Type: `string`
- Required: Yes
- Meaning: Short output identifier.

##### `type`
- Type: `string`
- Required: Yes
- Meaning: Abstract semantic type.
- Examples:
  - `Alert`
  - `ControlCommand`
  - `Text`
  - `Boolean`

##### `description`
- Type: `string`
- Required: Yes
- Meaning: Human-readable explanation of the output.

---

### 4.6 `constraints`
- Type: `object`
- Required: No
- Meaning: High-level non-functional requirements that may affect later placement (D2) or implementation (D3).
- This field is intentionally lightweight in v0.
- Suggested subfields:
  - `latency`
  - `privacy`
  - `energy`
  - `network`
- Good examples:
  - `"low latency"`
  - `"high privacy"`
  - `"limited energy budget"`
  - `"unstable network"`

This field should NOT contain:
- exact deployment locations
- protocol settings
- topic names
- hardware driver details

---

## 5. Example

```json
{
  "task_id": "fall_alarm_case_001",
  "target": "Detect a fall event and send an alarm notification.",
  "remarks": [
    "The system is deployed in a home environment.",
    "Only abnormal events need to trigger notifications."
  ],
  "inputs": [
    {
      "name": "camera_stream",
      "type": "ImageFrame",
      "description": "Video frames captured by the home camera.",
      "source": "device"
    }
  ],
  "outputs": [
    {
      "name": "alarm_message",
      "type": "Alert",
      "description": "An alarm notification sent to the caregiver."
    }
  ],
  "constraints": {
    "latency": "low",
    "privacy": "high",
    "energy": "medium",
    "network": "unstable"
  }
}
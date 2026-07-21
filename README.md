# Drone & Fleet Management

This project provides two classes:

- `drone`: models an individual drone with an ID, model, battery level, status, and mission count.
- `Fleet`: manages a collection of `drone` objects and can report overall fleet statistics.

## Files

- `DroneFleetManager.ipynb`: contains the `drone` and `Fleet` classes.

## Classes

### `drone`

Represents a single drone.

#### Initialization

```python
drone(drone_id, model, battery=100, status="Normal", missions_flown=0)
```

- `drone_id` (any): identifier for the drone (used as the fleet key)
- `model` (any): drone model name/number
- `battery` (int/float, default `100`): battery percentage
- `status` (str, default `"Normal"`): textual status
- `missions_flown` (int, default `0`): missions completed

#### Methods

- `assign_mission(battery_cost)`
  - If `battery < battery_cost`, returns `None`.
  - Otherwise:
    - increments `missions_flown`
    - subtracts `battery_cost` from `battery`

- `recharge(target=100)`
  - Sets `battery` to `target`.

- `check_battery()`
  - Prints the battery percentage.
  - Prints `"WARNING: CRITICAL BATTERY"` when `battery <= 15`.

- `status_level()`
  - Updates `self.status` based on current battery and returns a message:
    - `<= 25` → `"Critical"`
    - `<= 50` → `"Low"`
    - `<= 80` → `"Normal"`
    - `> 80` → `"Excellent"`

- `__str__()`
  - Returns a formatted multi-line “Drone Report” string.

---

### `Fleet`

Manages multiple drones.

#### Initialization

```python
Fleet()
```

Creates an empty fleet:

- `self.drones`: dictionary mapping `drone_id` → `drone` instance

#### Methods

- `add_drone(drone_id, drone)`
  - Adds/overwrites the drone under `drone_id`
  - Returns `self` for chaining.

- `remove_drone(drone_id)`
  - Deletes the drone from the fleet if it exists.

- `fleet_status()`
  - Prints `__str__()` for each drone in the fleet.

- `lowest_battery()`
  - Returns the `drone_id` with the lowest battery.
  - If the fleet is empty, returns `None`.

- `most_missions()`
  - Returns the `drone_id` with the most missions flown.
  - If the fleet is empty, returns `None`.

- `__str__()`
  - Returns a formatted multi-line “Fleet Report” string including:
    - number of drones
    - lowest battery drone (ID + battery%)
    - MVP (ID + missions flown)
    - total missions across all drones

---

## Example Usage

```python
# Create drones
d1 = drone(drone_id=1, model="XJ-200", battery=65)
d2 = drone(drone_id=2, model="AeroLite", battery=30)

# Create a fleet and add drones
f = Fleet()
f.add_drone(1, d1)
f.add_drone(2, d2)

# Assign missions
f.drones[1].assign_mission(battery_cost=20)  # battery becomes 45
result = f.drones[2].assign_mission(battery_cost=40)  # returns None if battery < 40

# Recharge and status checks
f.drones[2].recharge(100)
print(f.drones[2].status_level())

# Print reports
print(f)                 # calls Fleet.__str__()
f.fleet_status()        # prints each drone report
print(f.lowest_battery())
print(f.most_missions())
```

## Notes / Behavior Details

- `assign_mission(battery_cost)` does not automatically update `status`; call `status_level()` separately if you want status updated based on the new battery.
- `check_battery()` prints warnings directly (it does not return a value).

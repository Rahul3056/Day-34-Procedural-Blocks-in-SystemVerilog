

### **Day 34: Procedural Blocks in SystemVerilog**

---

#### **1. Overview**

Procedural blocks in SystemVerilog define sequences of operations that are executed either once or repeatedly, based on simulation events such as time, signal changes, or conditions. They are used to describe both **sequential behavior in design** (RTL) and **simulation stimulus in testbenches**.

SystemVerilog improves on Verilog by introducing new procedural blocks that provide **clear intent and better synthesis support**.

---

#### **2. Types of Procedural Blocks**

SystemVerilog supports the following procedural blocks:

1. `initial`
2. `always`
3. `always_comb`
4. `always_ff`
5. `always_latch`

Each has different use cases and synthesis/simulation implications.

---

#### **3. `initial` Block**

- Executes **only once** at time 0 (simulation time).
- Primarily used in **testbenches** for applying stimulus.
- **Not synthesizable** in RTL design.

**Example:**

```systemverilog
initial begin
    a = 0;
    b = 1;
    #10 a = 1;
end
```

---

#### **4. `always` Block**

- Executes **repeatedly** whenever any signal in the sensitivity list changes.
- Used in **Verilog** for both combinational and sequential logic.
- In SystemVerilog, its use is discouraged in favor of more specific blocks.

**Syntax:**

```systemverilog
always @(a or b) begin
    y = a & b;
end
```

**Problem:** If sensitivity list is incomplete or incorrect, simulation mismatch can occur.

---

#### **5. `always_comb` Block**

- Executes like `always @(*)`, but with **automatically inferred sensitivity list**.
- Ensures correct simulation behavior for **combinational logic**.
- No unintended latches if all outputs are assigned.

**Syntax:**

```systemverilog
always_comb begin
    y = a ^ b;
end
```

**Benefits:**
- No need to manually manage sensitivity list
- Catches accidental latch inference at compile time

---

#### **6. `always_ff` Block**

- Used for describing **synchronous sequential logic** (flip-flop behavior).
- Requires a **clock** (and optionally reset) in the sensitivity list.
- Only **non-blocking assignments (`<=`)** are allowed inside.

**Syntax:**

```systemverilog
always_ff @(posedge clk or negedge rst_n) begin
    if (!rst_n)
        q <= 0;
    else
        q <= d;
end
```

**Benefits:**
- Enforces flip-flop-style coding
- Compiler enforces correct usage (e.g., no blocking assignments)

---

#### **7. `always_latch` Block**

- Models **level-sensitive latches**.
- Sensitivity list inferred automatically.
- Requires at least one condition to be evaluated; otherwise, latch is inferred.

**Syntax:**

```systemverilog
always_latch begin
    if (en)
        q = d;
end
```

**Note:** Should be used **only when latch behavior is intended**. Otherwise, prefer `always_comb` to avoid unintended latch inference.

---

#### **8. Comparison Summary**

| Block Type     | Purpose               | Sensitivity List     | Synthesizable | Notes                            |
|----------------|------------------------|-----------------------|----------------|----------------------------------|
| `initial`      | Simulation-only init   | Time 0                | No             | Testbench use only               |
| `always`       | General behavior       | Manual                | Yes            | Replaced by SV-specific blocks   |
| `always_comb`  | Combinational logic    | Automatic             | Yes            | Safe and preferred               |
| `always_ff`    | Sequential (flip-flop) | Manual (clock)        | Yes            | Enforces good coding practices   |
| `always_latch` | Level-sensitive latch  | Automatic             | Yes            | Use only if latch is intentional |

---

#### **9. Use in RTL vs Testbench**

- **Design (RTL)**: Use `always_comb`, `always_ff`, `always_latch` for safe and synthesizable behavior.
- **Testbench**: Use `initial` and general `always` blocks to apply stimulus, monitor outputs, and drive simulation events.

---

#### **10. Best Practices**

- Prefer `always_comb`, `always_ff`, and `always_latch` over general `always`.
- Avoid `initial` blocks in synthesizable RTL.
- Use non-blocking assignments (`<=`) inside `always_ff` to model proper flip-flop behavior.
- Assign all outputs inside `always_comb` to prevent latch inference.
- Use explicit resets in sequential logic to avoid issues during synthesis or simulation.

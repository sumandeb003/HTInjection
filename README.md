<img title="This Week's Update" alt="Alt text" src="WIP.jpeg">

# Weekly Research Progress

<details>
<summary> 
  
### Update as of Oct 2, 2023 

</summary>
After my experience inserting trojans in the Ariane SoC, I realized that it is always better to insert trojans into a hardware design that has a testbench written for it. It will reduce a lot of my efforts while verifying the functionality of the trojan(s) inserted. I went through the code base of [Ariane](https://github.com/lowRISC/ariane/tree/master). Only 5 of the modules used in Ariane have testbenches written for them. So, I will target them for trojan insertion. These modules and their testbenches are:

1. [`axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_delayer.sv) - [`tb_axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_delayer.sv)
2. [`axi_id_remap.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_id_remap.sv) - [`tb_axi_id_remap.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_id_remap.sv)
3. [`axi_lite_to_axi.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_lite_to_axi.sv) - [`tb_axi_lite_to_axi.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_lite_to_axi.sv)
4. [`axi_lite_xbar.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_lite_xbar.sv) - [`tb_axi_lite_xbar.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_lite_xbar.sv)
5. [`axi_to_axi_lite.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_to_axi_lite.sv) - [`tb_axi_to_axi_lite.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_to_axi_lite.sv)

 
### [`axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_delayer.sv) 


This module is designed to introduce random delays to various AXI channels.

<details>
<summary> 
  
#### Source code: `axi_delayer`

</summary> 

```verilog
module axi_delayer #(
    parameter type aw_t = logic,
    parameter type w_t  = logic,
    parameter type b_t  = logic,
    parameter type ar_t = logic,
    parameter type r_t  = logic,
    parameter bit StallRandomOutput = 0,
    parameter bit StallRandomInput  = 0,
    parameter int FixedDelayInput   = 1,
    parameter int FixedDelayOutput  = 1
) (
    input  logic clk_i,   // Clock
    input  logic rst_ni,  // Asynchronous reset active low
    // input side
    input  logic aw_valid_i,
    input  aw_t  aw_chan_i,
    output logic aw_ready_o,

    input  logic w_valid_i,
    input  w_t   w_chan_i,
    output logic w_ready_o,

    output logic b_valid_o,
    output b_t   b_chan_o,
    input  logic b_ready_i,

    input  logic ar_valid_i,
    input  ar_t  ar_chan_i,
    output logic ar_ready_o,

    output logic r_valid_o,
    output r_t   r_chan_o,
    input  logic r_ready_i,

    // output side
    output logic aw_valid_o,
    output aw_t  aw_chan_o,
    input  logic aw_ready_i,

    output logic w_valid_o,
    output w_t   w_chan_o,
    input  logic w_ready_i,

    input  logic b_valid_i,
    input  b_t   b_chan_i,
    output logic b_ready_o,

    output logic ar_valid_o,
    output ar_t  ar_chan_o,
    input  logic ar_ready_i,

    input  logic r_valid_i,
    input  r_t   r_chan_i,
    output logic r_ready_o
);
    // AW
    ready_valid_delay #(
      .StallRandom ( StallRandomInput ),
      .FixedDelay  ( FixedDelayInput  ),
      .payload_t   ( aw_t             )
    ) i_ready_valid_delay_aw (
      .clk_i     ( clk_i      ),
      .rst_ni    ( rst_ni     ),
      .payload_i ( aw_chan_i  ),
      .ready_o   ( aw_ready_o ),
      .valid_i   ( aw_valid_i ),
      .payload_o ( aw_chan_o  ),
      .ready_i   ( aw_ready_i ),
      .valid_o   ( aw_valid_o )
    );

    // AR
    ready_valid_delay #(
      .StallRandom ( StallRandomInput ),
      .FixedDelay  ( FixedDelayInput  ),
      .payload_t   ( ar_t             )
    ) i_ready_valid_delay_ar (
      .clk_i     ( clk_i      ),
      .rst_ni    ( rst_ni     ),
      .payload_i ( ar_chan_i  ),
      .ready_o   ( ar_ready_o ),
      .valid_i   ( ar_valid_i ),
      .payload_o ( ar_chan_o  ),
      .ready_i   ( ar_ready_i ),
      .valid_o   ( ar_valid_o )
    );

    // W
    ready_valid_delay #(
      .StallRandom ( StallRandomInput ),
      .FixedDelay  ( FixedDelayInput  ),
      .payload_t   ( w_t              )
    ) i_ready_valid_delay_w (
      .clk_i     ( clk_i      ),
      .rst_ni    ( rst_ni     ),
      .payload_i ( w_chan_i   ),
      .ready_o   ( w_ready_o  ),
      .valid_i   ( w_valid_i  ),
      .payload_o ( w_chan_o   ),
      .ready_i   ( w_ready_i  ),
      .valid_o   ( w_valid_o  )
    );

    // B
    ready_valid_delay #(
      .StallRandom ( StallRandomOutput ),
      .FixedDelay  ( FixedDelayOutput  ),
      .payload_t   ( b_t               )
    ) i_ready_valid_delay_b (
      .clk_i     ( clk_i      ),
      .rst_ni    ( rst_ni     ),
      .payload_i ( b_chan_i   ),
      .ready_o   ( b_ready_o  ),
      .valid_i   ( b_valid_i  ),
      .payload_o ( b_chan_o   ),
      .ready_i   ( b_ready_i  ),
      .valid_o   ( b_valid_o  )
    );

    // R
     ready_valid_delay #(
      .StallRandom ( StallRandomOutput ),
      .FixedDelay  ( FixedDelayOutput  ),
      .payload_t   ( r_t               )
    ) i_ready_valid_delay_r (
      .clk_i     ( clk_i      ),
      .rst_ni    ( rst_ni     ),
      .payload_i ( r_chan_i   ),
      .ready_o   ( r_ready_o  ),
      .valid_i   ( r_valid_i  ),
      .payload_o ( r_chan_o   ),
      .ready_i   ( r_ready_i  ),
      .valid_o   ( r_valid_o  )
    );

endmodule
```

**Parameters:**

  - `aw_t`, `w_t`, `b_t`, `ar_t`, `r_t`: These are the data types for the various AXI channels. In AXI, there are separate channels for address write (`aw`), data write (`w`), write response (`b`), address read (`ar`), and read data (`r`).

  - `StallRandomOutput`, `StallRandomInput`: If set to 1, they introduce random stalls (delays) to the respective channels.

  - `FixedDelayInput`, `FixedDelayOutput`: They specify fixed delays for input and output channels.

**Ports:**

The module has corresponding ports for the input and the output sides for each AXI channel:

  - `clk_i` (Clock) and `rst_ni` (Reset): Standard control signals.

  - For each AXI channel (`aw`, `w`, `b`, `ar`, `r`), there are `valid`, `ready`, and `chan` (channel data) signals. The ones ending in `_i` are input signals to the delayer, and the ones ending in `_o` are output signals.

**Instantiations:**

For each AXI channel, there's an instantiation of a module named `ready_valid_delay`. This module presumably takes care of the actual delay mechanism - either random or fixed. As an example, I show the instantiation of the `ready_valid_delay` module for `aw` channel:

```verilog
ready_valid_delay #(
   .StallRandom ( StallRandomInput ),
   .FixedDelay  ( FixedDelayInput  ),
   .payload_t   ( aw_t             )
 ) i_ready_valid_delay_aw (
   .clk_i     ( clk_i      ),
   .rst_ni    ( rst_ni     ),
   .payload_i ( aw_chan_i  ),
   .ready_o   ( aw_ready_o ),
   .valid_i   ( aw_valid_i ),
   .payload_o ( aw_chan_o  ),
   .ready_i   ( aw_ready_i ),
   .valid_o   ( aw_valid_o )
 );
```
  - The parameter `StallRandom` is set to `StallRandomInput`, implying it's an input channel delay.
  - The `FixedDelay` parameter is set to `FixedDelayInput`, specifying the fixed delay count.
  - `payload_t` sets the datatype for the data being transmitted through this channel.
  - The connections then map the input signals of the `axi_delayer` to the input signals of the `ready_valid_delay` module and vice versa for the output.

**In a nutshell, the `axi_delayer` acts as a wrapper around multiple instances of `ready_valid_delay` modules. It essentially channels AXI data through these delay modules, allowing for the introduction of either fixed or random delays based on the parameters provided.**

<details>
<summary> 
  
#### Source code: `ready_valid_delay`

</summary> 

```verilog
// Copyright 2018 ETH Zurich and University of Bologna.
// Copyright and related rights are licensed under the Solderpad Hardware
// License, Version 0.51 (the "License"); you may not use this file except in
// compliance with the License.  You may obtain a copy of the License at
// http://solderpad.org/licenses/SHL-0.51. Unless required by applicable law
// or agreed to in writing, software, hardware and materials distributed under
// this License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
// CONDITIONS OF ANY KIND, either express or implied. See the License for the
// specific language governing permissions and limitations under the License.

// Author: Florian Zaruba, zarubaf@iis.ee.ethz.ch
// Description: Delay (or randomize) AXI-like handshaking

module ready_valid_delay #(
    parameter bit   StallRandom = 0,
    parameter int   FixedDelay  = 1,
    parameter type  payload_t  = logic
)(
    input  logic     clk_i,
    input  logic     rst_ni,

    input  payload_t payload_i,
    output logic     ready_o,
    input  logic     valid_i,

    output payload_t payload_o,
    input  logic     ready_i,
    output logic     valid_o
);

    if (FixedDelay == 0 && !StallRandom) begin : pass_through
        assign ready_o = ready_i;
        assign valid_o = valid_i;
        assign payload_o = payload_i;
    end else begin

        localparam COUNTER_BITS = 4;

        typedef enum logic [1:0] {
            Idle, Valid, Ready
        } state_e;

        state_e state_d, state_q;

        logic       load;
        logic [3:0] count_out;
        logic       en;

        logic [COUNTER_BITS-1:0] counter_load;

        assign payload_o = payload_i;

        always_comb begin
            state_d = state_q;
            valid_o = 1'b0;
            ready_o = 1'b0;
            load    = 1'b0;
            en      = 1'b0;

            unique case (state_q)
                Idle: begin
                    if (valid_i) begin
                        load = 1'b1;
                        state_d = Valid;
                        // Just one cycle delay
                        if (FixedDelay == 1 || (StallRandom && counter_load == 1)) begin
                            state_d = Ready;
                        end

                        if (StallRandom && counter_load == 0) begin
                            valid_o = 1'b1;
                            ready_o = ready_i;
                            if (ready_i) state_d = Idle;
                            else state_d = Ready;
                        end
                    end
                end
                Valid: begin
                    en = 1'b1;
                    if (count_out == 0) begin
                        state_d = Ready;
                    end
                end

                Ready: begin
                    valid_o = 1'b1;
                    ready_o = ready_i;
                    if (ready_i) state_d = Idle;
                end
                default : /* default */;
            endcase

        end

        if (StallRandom) begin : random_stall
            lfsr_16bit #(
              .WIDTH ( 16 )
            ) i_lfsr_16bit (
                .clk_i          ( clk_i        ),
                .rst_ni         ( rst_ni       ),
                .en_i           ( load         ),
                .refill_way_oh  (              ),
                .refill_way_bin ( counter_load )
            );
        end else begin
            assign counter_load = FixedDelay;
        end

        counter #(
            .WIDTH      ( COUNTER_BITS )
        ) i_counter (
            .clk_i      ( clk_i        ),
            .rst_ni     ( rst_ni       ),
            .clear_i    ( 1'b0         ),
            .en_i       ( en           ),
            .load_i     ( load         ),
            .down_i     ( 1'b1         ),
            .d_i        ( counter_load ),
            .q_o        ( count_out    ),
            .overflow_o (              )
        );

        always_ff @(posedge clk_i or negedge rst_ni) begin
            if (~rst_ni) begin
                state_q <= Idle;
            end else begin
                state_q <= state_d;
            end
        end
    end

endmodule
```
The `ready_valid_delay` module is designed to introduce delays or randomization in a ready/valid handshake mechanism, commonly used in many data transfer protocols including AXI.

**Parameters:**
  - `StallRandom`: When set, introduces random delays.
  - `FixedDelay`: Specifies the fixed delay to be introduced.
  - `payload_t`: The data type of the data (or payload) being transferred.

**Ports:**
Standard handshaking signals are:

  - `valid_i`: Indicates that the sender has valid data.
  - `ready_o`: Indicates that the receiver can accept data.
  - `payload_i`: Data input.
  - `valid_o`: Indicates that the delayed data is now valid.
  - `ready_i`: Indicates that the next stage/module is ready to receive data.
  - `payload_o`: Data output.

**My Understanding of the Module Functionality:**

  - If there's no delay (neither fixed nor random), the module simply passes the signals through:

```verilog
if (FixedDelay == 0 && !StallRandom) begin : pass_through
     assign ready_o = ready_i;
     assign valid_o = valid_i;
     assign payload_o = payload_i;
 end
```
  - If delays are introduced, the module uses a finite state machine (FSM) with three states: `Idle`, `Valid`, and `Ready`.
    - *Idle State*: The module is waiting for the input to be valid (`valid_i` asserted).
    - *Valid State*: The delay counter is active and counting down.
    - *Ready State*: The data is now valid and ready to be transferred out.

  - The state of the FSM is stored in the `state_q` register, which is updated every clock cycle based on the next state logic (`state_d`).
  - If `StallRandom` is set, the module uses a 16-bit Linear Feedback Shift Register (`lfsr_16bit`) to generate a random value, which is then used as the delay. The LFSR provides pseudorandom sequences.
  - A counter is used to introduce the delay. It's decremented every cycle and checks if it has reached zero. The length of the delay is either the `FixedDelay` parameter or the output from the LFSR (for random delays).

**FSM Logic:**

1. ***Idle State***:

When the FSM is in the `Idle` state, it checks if the `valid_i` (input data is valid) signal is asserted. If it's not asserted, the FSM simply remains in the `Idle` state.

If `valid_i` is asserted, a few scenarios can happen:

  - *One Cycle Delay*: If there's supposed to be a one-cycle delay (`FixedDelay == 1`) or a random delay of one cycle is selected (`StallRandom && counter_load == 1`), then the FSM directly transitions to the `Ready` state. This effectively introduces a one-cycle delay between the input becoming valid (`valid_i` asserted) and the output being marked valid (`valid_o` asserted).
```verilog
// Just one cycle delay
if (FixedDelay == 1 || (StallRandom && counter_load == 1)) begin
    state_d = Ready;
end
```
So, if the sequence of progression is:

i) Cycle 1: Input is valid (valid_i is high). FSM transitions from Idle to Ready.
ii) Cycle 2: Output is valid (valid_o is high). The data is effectively delayed by one cycle.

  - *Immediate Transmission on Zero Random Delay*: In this scenario, the FSM checks if a random delay with a value of 0 is chosen. If so, it immediately asserts the `valid_o` signal without waiting for another cycle. However, the FSM only transitions to the `Ready` state if the external entity isn't ready to accept the data (`ready_i` is low). If the external entity is ready (`ready_i` is high), it directly moves back to the `Idle` state, effectively transmitting the data without any delay.
```verilog
if (StallRandom && counter_load == 0) begin
    valid_o = 1'b1;
    ready_o = ready_i;
    if (ready_i) state_d = Idle;
    else state_d = Ready;
end
```
So, for the sequence of this progression is:
i) Cycle 1: Input is valid (valid_i is high), and if the next module is ready (ready_i is high), the FSM stays in Idle, thereby not introducing any delay. If the next module isn't ready, it moves to Ready, waiting for it to become ready.

  - *Load Delay Counter*: For any other condition, the delay counter is loaded (load signal is asserted), and the FSM transitions to the Valid state.

2. ***Valid State***:

In this state, the delay counter is decremented (`en` signal is asserted). The FSM waits until the counter reaches zero.

Once the counter (`count_out) reaches zero, it means the delay duration has passed, and the FSM transitions to the Ready state.

3. ***Ready State***:

In the `Ready` state, the module's output becomes valid (`valid_o` is asserted), signifying that the data is now ready to be transmitted after the delay.

The FSM then checks if the external entity (or next module) is ready to receive data (`ready_i`). If `ready_i` is high, indicating the next module is ready to accept the data, the FSM transitions back to the `Idle` state. If not, the FSM remains in the Ready state until `ready_i` is asserted.

In conclusion, the FSM ensures that once the input data is valid, it either directly passes the data (for zero delay) or waits for the specified delay duration before marking the data as valid for the next entity in the pipeline.

**Overall, the `ready_valid_delay` module essentially introduces either a fixed or random delay in the propagation of data from its input to its output using a combination of state machine logic, counters, and optional pseudorandom generation.**

</details>

</details>

<details>
<summary> 
  
#### Trojan Ideas

</summary> 

1. **Delay After a Specific Data Value:** Imagine we want to introduce a delay only when a specific data (payload) value is observed.

*Modification*:

  - Introduce a parameter for the rare data value.
  - Delay only when this value is detected.

Resulting code modification in `ready_valid_delay`:
```verilog
parameter payload_t RARE_DATA_VALUE = 'hDEADBEEF; // Example rare data value

// ...

always_comb begin
    // ... (rest of the logic)
    if (payload_i == RARE_DATA_VALUE) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```
2. **Random Long Delay:** Occasionally, you might want to introduce a much longer delay than usual, which can be useful for testing timeout conditions.

*Modification*:

  - Use an LFSR or random number generator.
  - Trigger a longer delay if a very specific rare random value is generated.

Resulting code modification in `ready_valid_delay`:
```verilog
localparam RARE_LFSR_VALUE = 16'hFFFF; // Example rare LFSR value

// ...

if (i_lfsr_16bit.refill_way_bin == RARE_LFSR_VALUE) begin
    assign counter_load = 1000;  // Introduce a long delay of 1000 cycles
end
```

3. **Burst Delay:** Sometimes you might want to simulate a condition where once a delay is introduced, several consecutive transactions are also delayed.

*Modification*:

  - Add a burst counter that is activated on a rare condition.
  - Delay consecutive transactions until the burst counter is depleted.

Resulting code modification in `ready_valid_delay`:
```verilog
localparam BURST_COUNT = 5;
logic [COUNTER_BITS-1:0] burst_counter;

always_ff @(posedge clk_i or negedge rst_ni) begin
    if (~rst_ni) burst_counter <= 0;
    else if (load && burst_counter == 0) burst_counter <= BURST_COUNT;
    else if (burst_counter > 0) burst_counter <= burst_counter - 1;
end

always_comb begin
    // ... (rest of the logic)
    if (burst_counter > 0) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```

**I thought of some rare conditions that can be used to trigger the above trojans. They are as follows:**

1. **Echo Delay:** Imagine introducing a delay when the same data value appears consecutively. This can be interesting for testing repetitive data patterns.

*Modification*: 

  - Store the last data value.
  - Check if the current data matches the previous one.
  - 
Resulting code modification in `ready_valid_delay`:
```verilog
payload_t last_data;

always_ff @(posedge clk_i or negedge rst_ni) begin
    if (~rst_ni) last_data <= '0;
    else last_data <= payload_i;
end

always_comb begin
    // ... (rest of the logic)
    if (payload_i == last_data) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```

2. **Delay on Rare Sequence:** Introduce a delay when a rare sequence of valid signals is detected, such as a pattern of alternating valids (1, 0, 1, 0, 1).

*Modification*:
  - Implement a small FSM or counter to detect the pattern.

Resulting code modification in `ready_valid_delay`:

```verilog
logic [2:0] valid_sequence;  // Stores a 3-bit pattern

always_ff @(posedge clk_i or negedge rst_ni) begin
    if (~rst_ni) valid_sequence <= '0;
    else valid_sequence <= {valid_sequence[1:0], valid_i};
end

always_comb begin
    // ... (rest of the logic)
    if (valid_sequence == 3'b101) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```

3. **Power-of-Two Delay:** A delay is introduced only when the data is a power of two. This can be useful to see how your system responds to specific data patterns.

*Modification*:
  - Check if data is a power of two.

Resulting code modification in `ready_valid_delay`:

```verilog
function logic is_power_of_two(payload_t data);
    return (data != 0) && ((data & (data - 1)) == 0);
endfunction

always_comb begin
    // ... (rest of the logic)
    if (is_power_of_two(payload_i)) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```

4. **Rare Parity Delay:** Introduce a delay only when the data has even parity (rare if your data is generally odd-parity).

*Modification*:
  - Check the parity of the data.

Resulting code modification in `ready_valid_delay`:

```verilog
function logic has_even_parity(payload_t data);
    int num_ones = $countones(data);
    return (num_ones % 2 == 0);
endfunction

always_comb begin
    // ... (rest of the logic)
    if (has_even_parity(payload_i)) begin
        state_d = Valid;
        load = 1'b1;
    end
end
```

</details>

</details>


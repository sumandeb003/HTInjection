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

<details>
<summary> 
  
### [`axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_delayer.sv) 

</summary>
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
    - *Idle State*: Awaiting for the valid signal to be asserted.
    - *Valid State*: Waiting for the delay to expire.
    - *Ready State*: The data is now valid and ready to be transferred out.

  - The state of the FSM is stored in the `state_q` register, which is updated every clock cycle based on the next state logic (`state_d`).
  - If `StallRandom` is set, the module uses a 16-bit Linear Feedback Shift Register (`lfsr_16bit`) to generate a random value, which is then used as the delay. The LFSR provides pseudorandom sequences.
  - A counter is used to introduce the delay. It's decremented every cycle and checks if it has reached zero. The length of the delay is either the `FixedDelay` parameter or the output from the LFSR (for random delays).
</details>

</details>

</details>

</details>

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
  
#### Source code

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

For each AXI channel, there's an instantiation of a module named ready_valid_delay. This module presumably takes care of the actual delay mechanism - either random or fixed. 

</details>

</details>

</details>

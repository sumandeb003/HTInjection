<img title="This Week's Update" alt="Alt text" src="WIP.jpeg">

# Weekly Research Progress

<details>
<summary> 
  
### Update as of Oct 2, 2023 

</summary>
After my experience inserting trojans in the Ariane SoC, I realized that it is always better to insert trojans into a hardware design that has a testbench written for it. It will reduce a lot of my efforts while verifying the functionality of the trojan(s) inserted. I went through the code base of the [Ariane SoC](https://github.com/lowRISC/ariane/tree/master). Only 5 of the [AXI](https://github.com/pulp-platform/axi/tree/de1af467229315ee6af31fea96664c7aae5638a9) modules used in Ariane have testbenches written for them. So, I will target them for trojan insertion. These modules and their testbenches are:

1. [`axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_delayer.sv) - [`tb_axi_delayer.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_delayer.sv)
2. [`axi_id_remap.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_id_remap.sv) - [`tb_axi_id_remap.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_id_remap.sv)
3. [`axi_lite_to_axi.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_lite_to_axi.sv) - [`tb_axi_lite_to_axi.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_lite_to_axi.sv)
4. [`axi_lite_xbar.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_lite_xbar.sv) - [`tb_axi_lite_xbar.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_lite_xbar.sv)
5. [`axi_to_axi_lite.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/src/axi_to_axi_lite.sv) - [`tb_axi_to_axi_lite.sv`](https://github.com/pulp-platform/axi/blob/de1af467229315ee6af31fea96664c7aae5638a9/test/tb_axi_to_axi_lite.sv)

</details>

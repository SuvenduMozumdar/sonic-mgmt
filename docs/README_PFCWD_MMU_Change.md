

## Testbed

### Topology 2
```
+-------------+      +--------------+      +-------------+       
| Keysight TX |------|   SONiC DUT  |------| Keysight RX | 
+-------------+      +--------------+      +-------------+ 

Keysight ports are connected via SONiC switch as shown in the illustration above.
```
*Fig. 2: Topology 2*

## Setup configuration

### DUT Configuration
•	Configure alpha value in DUT:

**_sudo mmuconfig -p [profile_name] -a [alpha_value]_**

•	To check all the profiles in DUT:

**_mmuconfig -l_**

### Keysight configuration
•	All Keysight ports should have the same bandwidth capacity.

•	Test specific configurations are mentioned in respective test cases.

### Test Case - Verify impact of MMU change on PFCWD

#### Test Objective

This test aims to verify changes in MMU settings of the DUT do not impact the current state of PFCWD and its functionality.

#### Test Topology

Refer to **_Fig. 2_** for the test topology.

#### Test Configuration

- On SONiC DUT configure the following:
  1. Enable watchdog with default storm detection time (400ms) and restoration time (2sec).
  2. Configure two lossless priorities **_Pi_**, **_Pm_** (0 <= i,m <= 7).
  3. To minimize configuration complexity, it is recommended that the SONiC DUT be configured as either Top of Rack (ToR) or Tier 0 (T0) switch with two VLAN interfaces.

- Configure following traffic items on the Keysight device:
  1. PFC pause storm 1: Persistent PFC pause frames from Keysight Rx port to the connected port of DUT. Priority of the PFC pause frames should be **_Pi_** and the inter-frame transmission interval should be lesser than per-frame pause duration.
  2. PFC pause storm 2: Persistent PFC pause frames from Keysight
        Rx port to the connected port of DUT. Priority of the PFC pause
        frames should be **_Pm_** and the inter-frame transmission interval should be lesser than
        per-frame pause duration.

#### Test Steps

1. Start **_PFC pause storm 1_**.
2. Verify the following by checking DUT's PFC watchdog state:
   * PFCWD action should have got triggered for priority **_Pi_**.
3. Change the alpha setting (rate reduction factor) for **_Pi_**.
4. Verify that DUT's PFC watchdog state should remain unchanged.
5. Stop **_PFC pause storm 1_**.
6. Start **_PFC pause storm 2_**.
2. Verify the following by checking DUT's PFC watchdog state:
   * PFCWD action should have got triggered for priority **_Pm_**.
3. Change the alpha setting for **_Pm_**.
4. Verify that DUT's PFC watchdog state should remain unchanged.
5. Stop **_PFC pause storm 2_**.
6. Repeat the test for other lossless priorities.
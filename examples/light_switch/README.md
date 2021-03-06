# Light switch example

@tag52810nosupport

This example demonstrates the mesh ecosystem that contains devices acting in the Node role
(also referred to as provisionee role).
It also demonstrates how to use Mesh models by using the [Generic OnOff model](@ref GENERIC_ONOFF_MODEL)
in an application.

**Table of contents**
- [Hardware requirements](@ref light_switch_example_hw_requirements)
- [Setup](@ref light_switch_example_setup)
    - [LED and button assignments](@ref light_switch_example_setup_leds_buttons)
- [Testing the example](@ref light_switch_example_testing)
    - [Evaluating using the static provisioner](@ref light_switch_example_testing_dk)
    - [Evaluating using the nRF Mesh mobile app](@ref light_switch_example_testing_app)
    - [Interacting with the boards](@ref light_switch_example_testing_interacting)

The example is composed of the following minor examples:
- Light switch server: A minimalistic server that implements a
[Generic OnOff Server model](@ref GENERIC_ONOFF_MODEL), which is used to
receive the state data and control the state of LED 1 on the board.
- Light switch client: A minimalistic client that implements two instances of a
[Generic OnOff client model](@ref GENERIC_ONOFF_MODEL).
When a user presses any of the buttons, an OnOff Set message is sent out to the
configured destination address.

You can use the [provisioner example](@ref md_examples_provisioner_README) to [evaluate the light switch example](@ref light_switch_example_testing_dk).
The provisioner example provides a simple static provisioner implementation that sets up the demonstration network.
The example provisions all the nodes in one mesh network. Additionally, it also configures
key bindings and publication and subscription settings of the mesh model instances on these nodes
to enable them to talk to each other.

@note For provisioning purposes, you can also use the @link_nrf_mesh_app.

The Generic OnOff Client/Server is used for manipulating the on/off state. Note that when the server has
a publish address set (as in this example), the server will publish any operation of its state change to
its publish address. More information about the Generic OnOff model can be found in the [Generic OnOff model documentation](@ref GENERIC_ONOFF_MODEL)
and [Generic OnOff server behavior documentation](@ref APP_ONOFF).

For a more detailed overview of the example structure and an introduction to various SDK APIs,
see the following pages:
- @subpage md_examples_light_switch_server_README
- @subpage md_examples_light_switch_client_README

The following figure gives the overall view of the mesh network that will be set up
by the static provisioner. Numbers in parentheses indicate the addresses that are assigned
to these nodes by the provisioner.

![Mesh network demonstration](images/mesh-nw-demo_r02.svg "Mesh network demonstration")

Both the light switch server and light switch client examples have provisionee role.
They support provisioning over Advertising bearer (PB-ADV) and GATT bearer (PB-GATT) and also support
Mesh Proxy Service (Server). Read more about the Proxy feature in @ref md_doc_user_guide_modules_provisioning_gatt_proxy.

@note The *Proxy Client* role is **not** supported.


---


## Hardware requirements @anchor light_switch_example_hw_requirements

You need at least two supported development kits for this example:

- One nRF52 development kit for the client.
- One or more nRF52 development kits for the servers.

Additionally, you need one of the following:
- One nRF52 development kit for the provisioner if you decide to use the [static provisioner example](@ref md_examples_provisioner_README).
- @link_nrf_mesh_app (@link_nrf_mesh_app_ios or @link_nrf_mesh_app_android) if you decide to provision
using the application.

See @ref md_doc_user_guide_mesh_compatibility for the supported development kits.


---


## Setup @anchor light_switch_example_setup

You can find the source code of this example and its minor examples in the following folder:
`<InstallFolder>/examples/light_switch`


### LED and button assignments @anchor light_switch_example_setup_leds_buttons

The buttons (1 to 4) are used to initiate certain actions, and the LEDs (1 to 4) are used to reflect
the status of actions as follows:

- Server:
    -  During provisioning process:
        - LED 3 and 4 blinking: Device identification active.
		- LED 1 to 4: Blink four times to indicate provisioning process is completed.
    - After provisioning and configuration is over:
        - LED 1: Reflects the value of OnOff state on the server.
				- LED ON: Value of the OnOff state is 1 (`true`).
				- LED OFF: Value of the OnOff state is 0 (`false`).

- Client:
    - During provisioning process:
        - LED 3 and 4 blinking: Device identification active.
		- LED 1 to 4: Blink four times to indicate provisioning process is completed.
    - After provisioning and configuration is over, buttons on the client are used to send OnOff Set
        messages to the servers:
        - Button 1: Send a message to the odd group (address: 0xC003) to turn on LED 1.
        - Button 2: Send a message to the odd group (address: 0xC003) to turn off LED 1.
        - Button 3: Send a message to the even group (address: 0xC002) to turn on LED 1.
        - Button 4: Send a message to the even group (address: 0xC002) to turn off LED 1.

- Provisioner:
  - See [provisioner example page](@ref provisioner_example_setup_leds_buttons) for the list
  of LED and button assignments if you use the static provisioner.


---


## Testing the example @anchor light_switch_example_testing

To test the light switch example, build the examples by following the instructions in
[Building the mesh stack](@ref md_doc_getting_started_how_to_build).

@note
If you have more than 40 boards for the server and decided to use the static provisioner example,
set `MAX_PROVISIONEE_NUMBER` (in `example_network_config.h`) to the number of boards available
and rebuild the provisioner example.

After building is complete, use one of the following methods, depending on the preferred
provisioning approach:
- [Evaluating using the static provisioner](@ref light_switch_example_testing_dk)
- [Evaluating using the nRF Mesh mobile app](@ref light_switch_example_testing_app)

Once the provisioning is complete, you can start [interacting with the boards](@ref light_switch_example_testing_interacting).

### Evaluating using the static provisioner @anchor light_switch_example_testing_dk

Complete the following steps:
1. Flash the examples by following the instructions in @ref md_doc_getting_started_how_to_run_examples,
including:
    1. Erase the flash of your development boards and program the SoftDevice.
    2. Flash the provisioner and the client firmware on individual boards and the server firmware on other boards.
2. After the reset at the end of the flashing process, press Button 1 on the provisioner board
to start the provisioning process:
    -# The provisioner provisions and configures the client and assigns the address 0x100 to the client
    node.
    -# The two instances of the OnOff client models are instantiated on separate secondary elements.
    For this reason, they get consecutive addresses starting with 0x101.
    -# The provisioner also provisions and configures the servers at random. It assigns them consecutive addresses starting with 0x201,
    and adds them to odd and even groups.
@note The sequence of provisioned devices depends on the sequence of received unprovisioned beacons.
@note You can use [RTT viewer](@ref segger-rtt) to view the RTT output generated by the provisioner.
The provisioner prints details about the provisioning and the configuration process in the RTT log.
3. Observe that the LED 1 on the provisioner board is turned ON when provisioner is scanning and provisioning a device.
4. Observe that the LED 2 on the provisioner board is turned ON when configuration procedure is in progress.
5. Wait until LED 1 on the provisioner board remains ON steadily for a few seconds, which indicates that
all available boards have been provisioned and configured.

If the provisioner encounters an error during the provisioning or configuration process for a certain node,
you can reset the provisioner to restart this process for that node.


### Evaluating using the nRF Mesh mobile app @anchor light_switch_example_testing_app

See @ref nrf-mesh-mobile-app "the information on the main Examples page" for detailed steps required
to provision and configure the boards using the nRF Mesh mobile app.

When using the nRF Mesh app with this example, the following naming convention is used in the app:
- The client board is `nRF5x Mesh Switch`.
- The server board is `nRF5x Mesh Light`.
    
The following model instances must be configured in the app for this example:
- For the `nRF5x Mesh Light` server board: Generic On Off Server.
- For the `nRF5x Mesh Switch` client board: Generic On Off Client.

At the end of the configuration process, the client example will be configured as follows:
- The Button 1 on the client board turns ON LED 1 on the corresponding server board or boards.
- The Button 2 on the client board turns OFF LED 1 on the corresponding server board or boards.

@note
You can also configure the publish address of the second Generic On Off client model instance.
To do this, repeat [step 3 from binding nodes](@ref nrf-mesh-mobile-app-binding) and [all steps from setting publication](@ref nrf-mesh-mobile-app-publication).
If you set the model instance's address to the Unicast Address of any server node, the client example will be configured as follows:
- The Button 3 on the client board turns ON LED 1 on the corresponding server board.
- The Button 4 on the client board turns OFF LED 1 on the corresponding server board.


### Interacting with the boards @anchor light_switch_example_testing_interacting

Once the provisioning and the configuration of the client node and at least one of the server nodes are complete,
you can press buttons on the client to see the LEDs getting toggled on the associated servers.
See [LED and button assignments](@ref light_switch_example_setup_leds_buttons) section.

If an RTT terminal is available and connected to the client, sending
the ASCII numbers `1`--`4` will have the same effect as pressing the buttons.

If you are using RTT log, you can also press Button 1 on the servers to locally toggle the state of their LED 1,
and the status reflecting this state will be sent to the client board. You can see the status printed in
the RTT log of the client board.

If any of the devices is powered off and back on, it will remember its flash configuration
and rejoin the network. For more information about the flash manager, see @ref md_doc_user_guide_modules_flash_manager.
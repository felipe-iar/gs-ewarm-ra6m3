# Getting Started with IAR Embedded Workbench for Arm and Renesas RA6M3

## Introduction
The Renesas Advanced (RA) 32-bit MCU family, based on the Arm® Cortex-M33, -M23 and -M4 processor cores, deliver stronger embedded security, high performance, and ultra-low power operation.

This guide provides an overview of the workflow for creating a simple “Hello LED” application with the IAR Embedded Workbench for Arm targeting the Renesas RA Evaluation Kit.

While this guide focuses on the RA6M3, it can be applied to similarly RA MCUs.

The information provided in this guide is based on the products available in July 2024.

## Products used in this guide
This section describes the hardware and software products used or mentioned in this guide.

| Product | Code name | Version
| - | - | - 
| [IAR Embedded Workbench for Arm](https://iar.com/ewarm) | EWARM | 9.50.2
| [IAR I-jet debug probe](https://iar.com/ijet) | I-JET | B
| [Renesas RA Smart Configurator](https://github.com/renesas/fsp/releases/tag/v5.4.0) | RASC | 5.4.0
| [Renesas RA6M3 Evaluation Kit](https://renesas.com/ek-ra6m3) | EK-RA6M3 | 1

>[!NOTE]
>This guide assumes IAR Embedded Workbench for Arm is installed and licensed.

## Setting up the evaluation board
The KE-RA6M3 Evaluation Kit includes the evaluation board and a micro-USB cable.

![image](https://github.com/user-attachments/assets/dea5a221-fde2-4cc9-a96b-f1c76b82bbb6)

### Jumper settings
Before powering the board, make sure that:
- `BOOT MODE` is set as `INTERNAL FLASH`

![image](https://github.com/user-attachments/assets/4c355b4a-15c1-4331-9487-a0100285ee0f)

- Jumper `J15` is set to `1-2`
  
![image](https://github.com/user-attachments/assets/6611cf4a-1e7a-4628-8fed-c97c2e105c8d)

### Connecting the board to the host computer
The Renesas EK-RA6M3 Evaluation Board conveniently comes with the SEGGER J-Link On-board debugger (`J10`), where the board can be powered directly from the USB bus, which is fully supported in the IAR Embedded Workbench for Arm. This is the option we are going to use in this guide.

Refer to the section 5.1 in the [EK-RA6M3 User's Manual](https://www.renesas.com/us/en/document/mat/ek-ra6m3-v1-users-manual) for more information on powering options.

>[!NOTE]
>The Evaluation Board also provides a MIPI20 header (`J20`) for attaching to an external debugging probe, allowing interfaces for JTAG, SWD and ETM (Embedded Trace Macrocell).
>
>![image](https://github.com/user-attachments/assets/b18211ff-8c32-47f1-8dec-889533ea3fff)

## Installing the RA Smart Configurator
The Renesas RA Smart Configurator (`setup_fsp_v5.4.0_rasc_v2024-04.exe`) can be obtained directly from its GitHub [release page](https://github.com/renesas/fsp/releases/tag/v5.4.0).

- Download [`setup_fsp_v5.4.0_rasc_v2024-04.exe`](https://github.com/renesas/fsp/releases/download/v5.4.0/setup_fsp_v5_4_0_rasc_v2024-04.exe)
- Install the RA Smart Configurator using the wizard's default options
- Launch `C:\Renesas\RA\sc_v2024-04_fsp_v5.4.0\eclipse\rasc.exe`

## Creating a new project
This section illustrates the steps for creating a new project with the RA Smart Configurator.

The first dialog window in the new project wizard will ask for __Project name__ and its __Location__. 

For this example use:
- __Project Name__: `Hello_LED`
- __Location__: _leave it to the default location (`%USERPROFILE%\FSPSmartConfigurator`)_

![rasc_15SRUoqOJp](https://github.com/user-attachments/assets/990b14bb-83b8-4e31-879b-3986813b930d)

- Click __Next__ to advance.



Now it is time to select the board and tool:
- __FSP Version__: `5.4.0`
- __Board__: `EK-RA6M3`
- __Language__: `C`
- __IDE Project Type__: `IAR EWARM [v9.40+]`

![rasc_CFPOz0vXVp](https://github.com/user-attachments/assets/6a8e182a-5435-4ca9-8bcc-668630127185)

- Click __Next__ to advance.



The next dialog select an __RTOS__:
- __RTOS Selection__: `No RTOS`

![rasc_HG2zR2vPOk](https://github.com/user-attachments/assets/420c161c-427c-4692-9ea8-21bf232f61fb)

- Click __Next__ to advance.

The final dialog is for __Template Selection__. Let's start from a clean slate:
- [ ] Bare Metal - Blinky
- [x] Bare Metal - Minimal

![rasc_4IKl9RV2Oa](https://github.com/user-attachments/assets/bd717e81-5de1-4e85-9b7a-286edc22b936)

- Finally click __Finish__.

Once the _New Project Wizard_ concludes, the main window will show up, displaying the project's Summary:

![rasc_WJd6GTH8ad](https://github.com/user-attachments/assets/b3a40411-7dbb-48b1-bea2-e61229a1887f)

It is recommended to get acquainted with the most important configurator options. Navigate throughout its configuration tabs.

| Tab | Description
| - | -
| Summary | Overview of the project: board, component versions, etc.
| BSP | Board Support Package 
| Clocks | Set up the MCU's clock tree
| Pins | MCU's pinout configurations
| Interrupts | Interface for controlling the aplication's interrupts
| Event Links | Allows linking events generated between peripherals
| Stacks | Graphical configuration of the sofware stacks used in the project
| Components | Provides a selectavble component tree for the project

According to the minimal template, the board’s default pins, the `r_ioport` driver, and clock tree selection for the EK-RA6M3 come pre-selected.

The following sub-sections will highlight the configuration tabs which are going to be used in the project.

### Configuring Pins
For the __Hello_LED__ project, we will use the GPIO `LED1` connected to `P403`:
- Select the __Pins__ tab
- Unfold the __Pin Selection__ tree
- Select `P4`, then `P403`
   - Make sure __Mode__: `Output mode (Initial Low)`
 
![rasc_HRepCPZ5ss](https://github.com/user-attachments/assets/286b7a0f-6f39-40a6-ad23-86d2ee2f50f8)

>[!NOTE]
>Later on, pins-related generated code can be found in the `g_bsp_pin_cfg.c` source file.

### Generate Project Content
- Once the project configuration is ready, click on __Generate Project Content__:

![image](https://github.com/user-attachments/assets/56458226-4ca0-4ef4-91cc-f06c6b15a180)

- Open the IAR Embedded Workbench workspace at `%USERPROFILE%\FSPSmartConfigurator\Hello_LED\Hello_LED.eww`.

### Flex Software Overview
By unfolding the nodes from the project tree in the _Workspace_ window, relevant files will be revealed:

![image](https://github.com/user-attachments/assets/58845cee-c407-4178-bd22-ee06adb204c4)

In a nutshell:

| Group | Contents 
| - | - 
| Build Configuration | Sources related to the Board Support Package (BSP)
| Components | Sources related to the components used by the software __Stacks__ used in the configuration
| Generated Data | Provides the `main.c` module with the `main()` function, the default application's entry point
| Program Entry | Provides the `hal_entry.c` module with the `hall_entry()` function, which is invoked by `main()` and from where the user-implemented code should start

![IarIdePm_xyicH1mZe7](https://github.com/user-attachments/assets/df89cbb1-6215-4135-ae29-686fa3d479b9)

>[!WARNING]
>Most of the generated sources are managed by the RA Smart Configurator and should not be directly modified.

### Quick Tour on the Project Options
In this section we will quickly walk through the relevant project options in the IAR Embedded Workbench.
- Go to __Project__ __Options__ (<kbd>Alt</kbd>+<kbd>F7</kbd>)
- Select __General Options__ and finally the __Target__ tab:

![IarIdePm_zBJCexvzxc](https://github.com/user-attachments/assets/b007bddd-8471-41a3-b441-efa65bc34b3f)

>[!NOTE]
>Here it is possible to verify that the RA Smart Configurator already selected the correct device for the board.

- Now select __C/C++ Compiler__ and the __Optimizations__ tab:

![IarIdePm_1bGNZ5sFeQ](https://github.com/user-attachments/assets/bd658725-416d-48eb-a455-73c47a93e69b)

>[!TIP]
>The project is created with optimization level set to __Low__ so that the compiler generates code better correlated to its originating source code, meaning an improved debugging experience. Once the application is ready, the level can be set to __High__.

- Next select __Build Actions__:

![IarIdePm_RKu50xDP35](https://github.com/user-attachments/assets/587ad279-d851-46d9-9e22-9ac5534b3fce)

>[!NOTE]
>Here scripts managed by the RA Smart Configurator are run prior compiling the application (__Pre-compile__ stage) and also at the end, during the __Post-link__ stage.
>You can select any of them and then click on the `Edit` button to check the detailed command lines.

- Now select __Debugger__ and the __Setup__ tab:

![IarIdePm_oeuHkbe1jr](https://github.com/user-attachments/assets/4bf091be-1e36-4737-a37a-b7670d7b7c42)

>[!NOTE]
>- The project comes ready for debugging with a J-Link or J-Trace probe. Here it is possible to select the desired driver for the debug probe.
>- The __Device Description File__ (`*.ddf`) contains the information about the MCU's registers and it is automatically set according on the __Target__ device selection.
>- In this tab the option __Run to__: `main` will set a breakpoint by default.

## Adding User Code
Let’s start by populating the `hal_entry()` function with code for a simple software-based delay:
```c
void hal_entry(void)
{
  static int count;
  while (1)
  {
    for (volatile uint32_t dly = 10000000; dly; dly--) {};
    count++;
  }
#if BSP_TZ_SECURE_BUILD
  /* Enter non-secure code */
  R_BSP_NonSecureEnter();
#endif
}
```

### Building
- Build the project by clicking on the __Make__ button (<kbd>F7</kbd>) in the main toolbar:

![image](https://github.com/user-attachments/assets/0da08d9c-e7f3-496d-82fe-c019f88efaab)

The project should build with no errors and no warnings as it can be seen in the __Build__ window:

![IarIdePm_u8Pb5J4qZ6](https://github.com/user-attachments/assets/9de966ea-404b-4bcb-91b0-fe90962b1c6d)

### Debugging
- Make sure `J10` is connected to the computer's USB port
- Click __Download and Debug__ (<kbd>Ctrl</kbd>+<kbd>D</kbd>)

![image](https://github.com/user-attachments/assets/07664b25-a2b7-4ac4-b140-fe7c5ac61b28)

The C Runtime startup will execute until the application reaches the `main()` function, where a breakpoint was set by default.

![IarIdePm_3fLUGnO08h](https://github.com/user-attachments/assets/555a910d-303a-4927-a9cd-32832a2196cb)

The IAR C-SPY Debugger provides its own toolbar for controlling the program's execution flow

- Click __Go__ (<kbd>F5</kbd>):

![image](https://github.com/user-attachments/assets/a014024f-e641-4ce2-8209-e6d451a3b687)

- Wait a few seconds and then click __Break__:

![image](https://github.com/user-attachments/assets/a5220af9-fd72-4df7-a772-84f9c334515e)

When we break the program execution, we will be inside our software delay in the `hal_entry()` function. The current statement is highlighted, as below:

![IarIdePm_0gITxPq8T1](https://github.com/user-attachments/assets/7f9462a2-5bee-402a-aee6-0827c853b889)

- Right-click on the `count++` statement and select __Add to Watch: 'count'__:

![image](https://github.com/user-attachments/assets/79b27f13-6042-458e-8070-45d6cf11b6fd)

- Resume (__Go__) and halt (__Break__) the application noticing a new window named __Watch 1__ with the variable `count` with its current value.

![IarIdePm_QhFyCW9J1v](https://github.com/user-attachments/assets/82a7b669-070f-4d76-8121-2317f4084415)

- Click __Stop Debugging__ (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd>).

![image](https://github.com/user-attachments/assets/2e0a3e09-9044-4251-9417-f71c52386ef8)

In this section we built and performed some basic debugging on our project. So now we are ready to start thinking about the applications details.

## `Hello_LED`
Now it is time for creating user code that will control one of the board's user LEDs (which uses a GPIO pin).

While it would be possible to control the GPIO directly from its memory mapped registers, the Flexible Software Package (FSP) simplifies the process through its API.

### Hello_LED - source code
- Replace the original `hal_entry()` function by the snippet below:
```c
void hal_entry(void)
{
  static int count;
  
  bsp_io_level_t     pin_level = BSP_IO_LEVEL_LOW;
  bsp_io_port_pin_t  pin       = BSP_IO_PORT_04_PIN_03;
    
  while (1)
  {
    /* Acccess to PFS register */
    R_BSP_PinAccessEnable();
    
    /* Write to this pin */
    R_BSP_PinWrite(pin, pin_level);
     
    /* Protect PFS registers */
    R_BSP_PinAccessDisable();
      
    /* Toggle level for next write */
    if (BSP_IO_LEVEL_LOW == pin_level)
    {
      pin_level = BSP_IO_LEVEL_HIGH;
    }
    else
    {
      pin_level = BSP_IO_LEVEL_LOW;
    }
     
    for (volatile uint32_t dly = 10000000; dly; dly--) {};
    count++;
  }
#if BSP_TZ_SECURE_BUILD
  /* Enter non-secure code */
  R_BSP_NonSecureEnter();
#endif
}
```
The `R_BSP_PinWrite()` function toggles the LED’s GPIO. The function is implemented in `bsp_io.h` and takes 2 parameters (`pin` and `level`). It can be reached by setting the cursor on top of its function name and pressing <kbd>F12</kbd>.

The `R_BSP_PinAccessEnable()` and `R_BSP_PinAccessDisable()` functions are used for ensuring safe operation regarding the pin configurations.
 
### Running Hello_LED
Rebuild and debug the project:
- __Make__ the project (<kbd>F7</kbd>)
- Then __Download and Debug__ (<kbd>Ctrl</kbd>+<kbd>D</kbd>)
- And finally run the application using __Go__ (<kbd>F5</kbd>).

LED1 should blink every second.

>[!TIP]
>Try commenting out the `R_BSP_PinAccessEnable()` and `R_BSP_PinAccessDisable()` function calls in the `hal_entry()` function. Then rebuild the project and watch what happens to the LED. It should not blink anymore.

### Detailing pins initialization
In a typical FSP project, the `r_ioport` component is added automatically. The pin initialization is performed in the `R_BSP_WarmStart()` function in the `hal_entry.c` module. The function is automatically invoked before the `main()` function.
```c
void R_BSP_WarmStart (bsp_warm_start_event_t event)
{
    if (BSP_WARM_START_RESET == event)
    {
#if BSP_FEATURE_FLASH_LP_VERSION != 0

        /* Enable reading from data flash. */
        R_FACI_LP->DFLCTL = 1U;

        /* Would normally have to wait tDSTOP(6us) for data flash recovery. Placing the enable here, before clock and
         * C runtime initialization, should negate the need for a delay since the initialization will typically take more than 6us. */
#endif
    }

    if (BSP_WARM_START_POST_C == event)
    {
        /* C runtime environment and system clocks are setup. */

        /* Configure pins. */
        R_IOPORT_Open(&IOPORT_CFG_CTRL, &IOPORT_CFG_NAME);

#if BSP_CFG_SDRAM_ENABLED

        /* Setup SDRAM and initialize it. Must configure pins first. */
        R_BSP_SdramInit(true);
#endif
    }
}
```

The `R_BSP_WarmStart()` function initializes all the pins configured in the RA Smart Configurator.
Details about the function’s execution can be inspected using a code breakpoint. 

- Click on the Code Editor's left curb inlined with the statement which invokes `R_IOPORT_Open()`. This will create a breakpoint symbolized by a red circle.

![image](https://github.com/user-attachments/assets/02b50c19-2f0c-4c7e-af0c-46be2005c034)

- Click __Reset__ in the Debug toolbar

![image](https://github.com/user-attachments/assets/8bdc1405-236b-46d1-b16a-0c3d9cb0cbd0)

- Click __Go__ (`F5`) to execute the program
- Once the execution reaches the breakpoint, __Step Into__ (`F11`) the `R_IOPORT_Open()` function:

![image](https://github.com/user-attachments/assets/6b398b36-e533-4bd1-b57d-6f61b951c22b)

## Improving `Hello_LED` with a tuned software delay
So far we implemented a very basic for-loop-based delay. The FSP's API privides a consistent function for software delay: `R_BSP_SoftwareDelay()`. Example:
```c
void hal_entry(void)
{
  static int count;
  
  bsp_io_level_t     pin_level = BSP_IO_LEVEL_LOW;
  bsp_io_port_pin_t  pin       = BSP_IO_PORT_04_PIN_03;
  
  const bsp_delay_units_t bsp_delay_units = BSP_DELAY_UNITS_MILLISECONDS;
  
  /* Set the blink frequency must be <= bsp_delay_units */
  const uint32_t freq_in_hz = 2;
  
  /* Calculate the delay in terms of bsp_delay_units */
  const uint32_t delay = bsp_delay_units / freq_in_hz;  
  
  while (1)
  {
    /* Acccess to PFS register */
    R_BSP_PinAccessEnable();
    
    /* Write to this pin */
    R_BSP_PinWrite(pin, pin_level);
     
    /* Protect PFS registers */
    R_BSP_PinAccessDisable();
      
    /* Toggle level for next write */
    if (BSP_IO_LEVEL_LOW == pin_level)
    {
      pin_level = BSP_IO_LEVEL_HIGH;
    }
    else
    {
      pin_level = BSP_IO_LEVEL_LOW;
    }
     
    //for (volatile uint32_t dly = 10000000; dly; dly--) {};
    /* BSP's software delay */
    R_BSP_SoftwareDelay(delay, bsp_delay_units);

    count++;
  }
#if BSP_TZ_SECURE_BUILD
  /* Enter non-secure code */
  R_BSP_NonSecureEnter();
#endif
}
```
You can test this approach if you like. However, software delays have some demerits. Go to the next section for learning how a timer interrupt can be used towards a far superior approach for when executing periodic processes.

## Improving `Hello_LED` with a timer interrupt
The Renesas RA MCU provides General Purpose Timers (GPTs). Let's use one timer for toggling the LED every 100 ms:
- Go back to the RA Smart Configurator
- Go to the __Stacks__ tab

![rasc_ScAWCIq8ij](https://github.com/user-attachments/assets/ed771bc1-081a-479a-8d68-31ecd6c4e726)

- Select __New Stack >__ → __Timers__ → __Timer, General PWM (r_gpt)__:

![image](https://github.com/user-attachments/assets/601af889-bdbe-4f71-b957-8bb73c3c0f76)

### Configure the parameters of the Timer stack
- Select the `g_timer0` stack
- Go to its __Propertiles__ tab and set them as below:

| Property | Value
| - | - 
| General->Mode | `Periodic`
| General->Period | `100`
| General->Period Unit | `Milliseconds`
| Interrupts->Callback | `g_timer0_callback`
| Interrupts->Overflow/Crest Interrupt Priority | Priority 1

![rasc_SzAJk8jzJU](https://github.com/user-attachments/assets/89791ad8-79df-42d0-9644-933d5a26c6dc)

### Reviewing the interrupt allocation
- Switch to the __Interrupts__ tab

![rasc_5f3aK5Hl19](https://github.com/user-attachments/assets/0d65d5d8-be55-4198-b2dd-5d388bd31704)

>[!NOTE]
>- The ISR `gpt_counter_overflow_isr()` is assigned to the `GPT0_COUNTER_OVERFLOW`.
>- The RA Smart Configurator generates interrupt handlers suffixed with `_isr()`. These handlers call their respective callback functions.

### Re-generating the project code
>[!WARNING]
>Before proceeding make sure there is no running instance of the IAR C-SPY Debugger, case in which, terminate it.

- Click __Generate Project Content__

![rasc_h2sCNY12bX](https://github.com/user-attachments/assets/07bf40f5-c881-4a2b-b84b-56fc9c79bac4)

The RA Smart Configurator will update the IAR Project Connection File (`buildinfo.ipcf`) with `r_gpt.h` and `r_timer_api.h`. Note that when a project is regenerated, all the files in the __Generated Data__ group will be overwritten:

![IarIdePm_aZbqmyquTz](https://github.com/user-attachments/assets/608f45d2-e3a1-4011-9e40-6c905882c01a)

The `hal_entry.c` file will not be overwritten while the RA Smart Configurator regenerates the project files, which means that we can continue our development from where we stopped.

>[!TIP]
>It is recommneded to use the `r_timer_api` functions for better maintainability.

### Implementing the Timer Interrupt Callback function
Let's implement `g_timer0_callback()` in accordance to its prototype.

- Select __Edit__ → __Find and Replace__ → __Find in Files__ (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd>)
   - Fill the __Find what:__ field with `g_timer0_callback`
   - __Look in:__ `Project files and all include files`

![IarIdePm_DAdrLTqYGO](https://github.com/user-attachments/assets/5f82e46b-b3b1-48dc-93c3-8e39e2fa67a1)

And we will find 3 occurences matching `g_timer0_callback` accross our project:

![IarIdePm_PtpGBhDvNZ](https://github.com/user-attachments/assets/50d72096-4e19-4002-abd2-ed93772bdbf5)

- Open the file `hal_data.h` and capture the function's signature.
- Once the signature is known (`void g_timer0_callback(timer_callback_args_t * p_args)`), go back to `hal_entry.c`module and provide an implementation as below, placed **before** the `hal_entry()` function:
```c
// etc.

uint32_t g_timer_overflow_flg = 0;

void g_timer0_callback(timer_callback_args_t * p_args）
{
  if (NULL != p_args）
  {
    g_timer_overflow_flg++;
  }
}

/******************************************************//**
 * main() is generated by the RA Configuration editor and
 * is used to generate threads if an RTOS is used.
 * This function is called by main() when no RTOS is used.
 *********************************************************/
void hal_entry(void)
{
// etc.
```

>[!NOTE]
>Some interrupts can use `p_args` to get parameters via pointers. In this example we are not making use of any additional parameters and will only check if `p_args` is NULL. Then `g_timer_overflow_flg` is updated so that we can know that an interrupt occurred.

Update `hal_entry()` as in the snipped below:
```c
void hal_entry (void) 
{
  static int count;
  bsp_io_level_t    pin_level = BSP_IO_LEVEL_LOW;
  bsp_io_port_pin_t pin       = BSP_IO_PORT_04_PIN_03;   
  fsp_err_t         err       = FSP_SUCCESS;
    
  err = g_timer0.p_api->open(&g_timer0_ctrl, &g_timer0_cfg);
  if (FSP_SUCCESS != err) { while (1); }
    
  err = g_timer0.p_api->start(&g_timer0_ctrl);   
  if (FSP_SUCCESS != err）{ while (1); }
    
  while (1) 
  {
    R_BSP_PinAccessEnable();
    R_BSP_PinWrite(pin, pin_level);
    R_BSP_PinAccessDisable();
  
    if (BSP_IO_LEVEL_LOW == pin_level)
    {
      pin_level = BSP_IO_LEVEL_HIGH;
    } else {
      pin_level = BSP_IO_LEVEL_LOW; 
    }
    
    while (g_timer_overflow_flg == 0);
    g_timer_overflow_flg = 0;
    count++;
  }
#if BSP_TZ_SECURE_BUILD
  /* Enter non-secure code */
  R_BSP_NonSecureEnter();
#endif
}
```

When the code above executes, __LED1__ will toggle precisely every 100 milliseconds.

### Debugging the timer interrupt callback function
- During a debug session, enable the graphical stack usage visualization: __View__ → __Stack__ → __Stack 1__

![image](https://github.com/user-attachments/assets/25c6aaf2-4bf4-421c-8034-a5e6827bec19)

## Configuring RASC as an external tool
When developing from the IAR Embedded Workbench IDE, it is possible to add the RA Smart Configurator as an external tool for easy access, so the FSP configuration can be accessed directly.

- In the IAR Embedded Workbench main menu choose: __Tools__ → __Configure Tools__ → __New__ and enter the following information:

- For __RA Smart Configurator v5.4.0__

| Item | Value
| - | -
| Menu Text | `RA Smart Configurator v.5.4.0`
| Command | Select `Browse...` and navigate to `C:\Renesas\RA\sc_v2024-04_fsp_v5.4.0\eclipse\rasc.exe`
| Argument | `-compiler IAR configuration.xml`
| Initial Directory | `$PROJ_DIR$`
| Tool Available | `Always`

- Create a __New__ item and, for __RA Device Partition Manager__ use the following:

| Item | Value
| - | -
| Menu Text | `RA Device Partition Manager`
| Command | Select `Browse...` and navigate to `C:\Renesas\RA\sc_v2024-04_fsp_v5.4.0\eclipse\rasc.exe`
| Argument | `-application com.renesas.cdt.ddsc.dpm.ui.dpmapplication configuration.xml "$TARGET_PATH$"`
| Initial Directory | `$PROJ_DIR$`
| Tool Available | `Always`

The RA Smart Configurator can now be launched from within the IDE main menu: __Tools__ → __RA Smart Configurator v5.4.0__.

An IAR Project Connection File (`*.ipcf`) must be set up in EWARM to build the RA FSP project. Select __Project__ → __Add Project Connection__ → __IAR Project Connection__ and browse to the project's `buildinfo.ipcf` so that the project is no connected to RASC.

## Importing projects from e2studio to IAR Embedded Workbench IDE
It is possible to import a project created on e2studio to the IAR Embedded Workbench IDE.

- Launch RASC and cancel the wizard
- In the main menu, select __File__ → __Import__ → __FSP Project...__ and use __Project type__ to migrate to `IAR EWARM 9.40+`

![image](https://github.com/user-attachments/assets/976f72a9-24e8-4b61-a547-090f8c403e52)


## Summary
This walkthrough has shown how easy it is to start developing with the IAR Embedded Workbench for Arm using the Renesas RA Smart Configurator along with the EK-RA6M3 Evaluation Board. 

The Renesas RA family offers powerful features. By using the RA Smart Configurator, it becomes quick and easy to graphically choose the project's desired software stacks and their respective configurations.

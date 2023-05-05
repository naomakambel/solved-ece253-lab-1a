Download Link: https://assignmentchef.com/product/solved-ece253-lab-1a
<br>
Starter Lab

Lab 1A is the starter lab, designed to introduce the development tools used for creating hardware/software interfaces. After completing Lab 1A, you will have created an RTL project using the <strong>Vivado </strong>design suite from Xilinx. Your basic RTL project will consist of the soft-core <strong>Microblaze</strong>[2] processor running on the <strong>Artix A7-100T development board</strong>[1] from Digilent. The Artix A7 board utilizes a Xilinx FPGA chip, the Artix 7. The Artix A7 board was previously named Nexys 4 DDR, and is identical to the Nexys 4 DDR in terms of functionality.

The Microblaze IP core is configurable, and permits creation of a system with different memory sizes, clock speeds and # of pipeline stages. The goal of this lab is to use the Vivado design Suite to create a custom hardware design for a processor and its peripherals, export the hardware to the Xilinx SDK, and then develop <strong>C </strong>programs to run on the custom processor. The <strong>C </strong>programs will gather timing information about the embedded system in terms of the number of clock cycles to execute different operations. Finally, timing uncertainties in the gathered data will be statistically analyzed.

<h1>1           Equipment</h1>

Throughout the course we will be creating Vivado RT (Register Transfer) projects, which are based around the block level diagram. An RT project refers to the level of abstraction selected for describing the design.

<strong>Hardware </strong>Nexys A7-100T Digilent Development board with Artix-7 FPGA

<strong>Software </strong>Vivado 2019.1

<strong>Software </strong>Xilinx SDK (with Vivado Integration)

The <strong>Lab Hardware Handout </strong>provides information about setting up the Artix A7 Development board with Vivado 2019.1

and the Xilinx SDK. It also contains information on interfacing the DDR2 memory with Microblaze, and general information which is useful for all of the labs. For this lab, the DDR2 memory will need to be a part of your design. Read the <strong>Lab Hardware Handout </strong>before starting the exercises.

<h1>2              Microblaze Configuration to be Explored</h1>

The configuration of the Microblaze processor provides various options, such as the size of the BRAM memory used by Microblaze, adding data and instruction caches and enabling debugging tools. For this lab, build Microblaze using the configuration instructions in the Lab hardware handout. Follow instructions in the <strong>Lab Hardware Handout </strong>to start Vivado and create a project containing a Microblaze processor along with DDR2 Memory. For this lab you should run your program in the DDR memory with caches enabled.

<h1>3           Adding Peripherals</h1>

The Microblaze processor provides a central processing unit for our embedded system to interact with the physical world.

The next step in the design of our system is to begin adding I/O peripherals. Follow instructions in the <strong>Lab Hardware Handout </strong>to add IP for: (1) Two AXI Timers and (2) LEDs. You should add the other peripherals in the hardware handout, but they are not used in this lab. Peripherals communicate with the processor using buses. In our design we are using the AXI bus. Buses are one of the design elements which face timing issues. As we complete the timing analysis of operations listed in Section 5, we begin to see when peripherals are competing for bus access.

<h1>4              Timing Analysis Guidelines</h1>

After building the hardware and generating bitstream in Vivado, export your hardware and launch SDK. Create a new “blank” Application Project in SDK. We have provided default source code files that you will need for this lab. Copy these files to the “src” directory of your Application Project. The main code for this project that will be edited by you is enclosed in timing.c. This code provides you with the basic structure for exercising different peripherals and for measuring the number of clock cycles.

Timing measurement on a computer is rather more complex than you might think. Abstractly, we can just read the timer, then do something and then read the new timer and voila… In real life, the memory access to the data and the instruction access are both non-zero time behaviors, the call to read the timer involves a context switch and stack update… In short, there is a lot of random overhead. In particular, timing a single operation or event is hard because you have so much random measurement overhead. One solution is to in-line write 5 events, 10 events, 20 events as so on – then do a regression to estimate the actual time it takes for events to run. A variable ‘Data’ has been setup in the default code to run such analysis on DDR read operations. Change the number of in-line calls in the code and fit a line (use linear regression) for 5, 15, 25, 40 in-line calls to estimate the measurement overhead and the per read timing of DDR memory. You can now use this estimation of average run time to offset your test measurements. This way you are estimating the actual run time of the function you want to test. Note the noise associated with this measurement. Do you see what you expect?

For the list below, measure each operation repeatedly using the built-in loop. Do not inline the timed operations, use the measurement overhead above to estimate the actual event times. Run the loop at least 10000 times to get a stab at some of the more rare event timings, for the USB port, run the loop 1000 times to keep the experiment time reasonable. Be careful not to simply increase the loop to much as the default data storage to the histogram can overwhelm the stack. If you want to run longer tests to catch rare events, you’ll need to either merge multiple runs in the histogram or perform the histogram locally to prevent storing the measurements.

A histogram function that counts the number of samples in uniformly spaced, equally sized bins is included in the code. You may choose an appropriate value for total number of bins and use this function for statistically analyzing your data. Be sure to use enough bins to display the variance in the data.

<strong>Be careful about the number of variables you create on the stack – if the code suddenly stops running or halts, it is likely that you exceeded the available stack size, exceeding the data segment will cause loader or compiler errors.</strong>

<h1>5           List of Operations to be Timed</h1>

<ol>

 <li>Timing of addition using integers</li>

 <li>Timing of addition using floating point</li>

 <li>Timing of writing the LEDs to turn on or off</li>

 <li>Timing of reading a word from the DDR2 memory at a random location</li>

 <li>Timing of writing to the USB Port: (<strong>You need to run the loop for this part only 1000 times</strong>)

  <ul>

   <li>Write a floating point number using <em>printf() </em>(Always use printf() with a “
”, eg.: printf(“A
”) to avoid BRAM overflow. fprintf also can work if ”
” is not working)</li>

   <li>Write a string of 10 characters using <em>xil </em><em>printf()</em></li>

  </ul></li>

</ol>

While you are gathering data, another method in main, extra method() is also running. This extra method() adds a source of resource contention, which will change the time for communicating with these peripherals.

<h1>6           Reporting Results and Observations</h1>

It should be taken into consideration that embedded systems are targeted at tightly constrained environments, with multi-kHz sampling rates, multiple interrupt sources, and relatively small memories with realistic bus environments. Write a two-page report, including the following results in your report:

<ol>

 <li>Report the regression estimate of overhead and DDR timing from section 4.</li>

 <li>Include histograms of the measurement data for the operations listed in Section 5.</li>

 <li>Plot a CDF for the DDR access timing, What is the expected value for this timing? What is a 95% confidence interval for this measurement?</li>

 <li>What do you observe about the magnitude of measured values and the timing uncertainties associated with different operations?</li>

 <li>Did you have any outliers in your results? If so, what could have caused these values?</li>

</ol>
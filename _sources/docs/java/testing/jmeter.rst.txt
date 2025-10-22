Apache JMeter
=============

#. **What is JMeter?**

   Apache JMeter is an open‑source, Java‑based performance testing tool 
   developed by the Apache Software Foundation that is used to test and 
   measure the performance of applications, servers, and network components

#. **Main Purpose:**

   JMeter is designed to perform load testing, stress testing, and performance analysis. It simulates multiple users accessing a web service, API, or database concurrently to evaluate how well the system performs under various loads.​

#. **Key Features:**

   - Supports testing of multiple protocol types such as HTTP, HTTPS, SOAP, REST, FTP, JDBC (databases), LDAP, JMS, and TCP.​
   - Provides a GUI-based Test IDE for quickly creating, debugging, and visualizing test plans while also supporting command-line execution for CI pipelines.​
   - Supports multi-threaded testing, meaning it can simulate numerous concurrent users to mimic real-world load scenarios.
   - Offers extensive reporting and visualization tools, including dynamic HTML and graph-based performance reports.​
   - Extensible architecture — new plugins and samplers can be easily added to support additional protocols or custom behavior.​

#. Installation and Setup

   - Download JMeter from `Official Website <https://jmeter.apache.org/download_jmeter.cgi>`_.
   - Download Binary file: apache-jmeter-5.6.3.zip
   - Place this to C Drive and Extract
   - Go to Extrcated Jmeter Folder -> bin
   - Take bin directory to command prompt
   - Run the bat file.
    
     .. code-block:: bash
        
         jmeter

     .. code-block:: bash

         WARN StatusConsoleListener The use of package scanning to locate plugins is deprecated and will be removed in a future release
         ================================================================================
         Don't use GUI mode for load testing !, only for Test creation and Test debugging.
         For load testing, use CLI Mode (was NON GUI):
            jmeter -n -t [jmx file] -l [results file] -e -o [Path to web report folder]
         & increase Java Heap to meet your test requirements:
            Modify current env variable HEAP="-Xms1g -Xmx1g -XX:MaxMetaspaceSize=256m" in the jmeter batch file
         Check : https://jmeter.apache.org/usermanual/best-practices.html
         ================================================================================     

#. **Reference Link:**

   - `Youtube Video <https://youtu.be/eqZORQpOuZA?si=DWS_nImNMzKP5uPU>`_ 
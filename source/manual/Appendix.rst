Appendix
=============
.. toctree:: 
    :maxdepth: 5

.. _error-codes:

Appendix A: API Return Code Reference Table
-------------------------------------------

The following table lists the error codes returned by the Camera SDK API functions, along with their descriptions and troubleshooting recommendations.

.. list-table:: Error Code Reference Table
   :widths: 15 25 60
   :header-rows: 1
   
   * - Error Code
     - Description
     - Troubleshooting
   * - ``-1``
     - Operation Failed
     - Verify that parameters are correctly passed
   * - ``-2``
     - Internal Error
     - Camera internal logic error, contact support, check logs
   * - ``-3``
     - Unknown Error
     - Camera internal error, contact support, check logs
   * - ``-4``
     - Function Not Supported
     - Feature not supported, verify parameter compatibility
   * - ``-5``
     - Initialization Incomplete
     - Check if SDK is initialized and camera is initialized
   * - ``-6``
     - Not Enabled
     - Power cycle device, check if power LED is solid green
   * - ``-9``
     - Network Transport Module Creation Failed
     - Check camera status, power supply, and communication
   * - ``-10``
     - Network Transport Module Initialization Failed
     - Check camera status, power supply, and communication
   * - ``-13``
     - Network Transport Module Not Found
     - Check camera status, power supply, and communication
   * - ``-14``
     - Network Camera Kernel Filter Driver Initialization Failed
     - Check camera status, power supply, and communication
   * - ``-18``
     - Received Unknown Network Image Type
     - Verify image type setting is within ``dtu_type_t`` enum
   * - ``-19``
     - Received Invalid Network Image Type
     - Verify image type setting is within ``dtu_type_t`` enum
   * - ``-20``
     - Received Unknown Network Data Type
     - Verify data type setting is within ``cloudType`` enum
   * - ``-21``
     - Received Invalid Network Data Type
     - Verify data type setting is within ``cloudType`` enum
   * - ``-22``
     - Lost Connection with Network Camera, Heartbeat Timeout
     - Restart camera, contact support to check logs
   * - ``-23``
     - Data Verification Error
     - (No troubleshooting provided)
   * - ``-24``
     - IP Address Error
     - Verify IP address is correct
   * - ``-25``
     - Command Transmission Exception
     - Check camera firmware version, restart camera
   * - ``-26``
     - Failed to Get Image Buffer
     - Check for zombie threads occupying image acquisition thread
   * - ``-27``
     - Camera Trigger Capture Exception
     - Check for zombie threads occupying image acquisition thread
   * - ``-28``
     - Failed to Get Camera Parameters
     - Check if .h header files match camera firmware version
   * - ``-31``
     - Enumeration Failed
     - Verify camera IP address is correct
   * - ``-32``
     - Camera Disconnected
     - Check power supply and communication connections

Error Classification
--------------------

Network Related Errors (``-9`` to ``-14``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Network Error Classification
   :widths: 20 40 40
   :header-rows: 1
   
   * - Error Code
     - Issue Description
     - Check Points
   * - ``-9``, ``-10``
     - Network Transport Module Creation/Initialization Failed
     - 1. Network connection status
         2. Firewall settings
         3. Network drivers
   * - ``-13``
     - Network Transport Module Not Found
     - 1. Ethernet cable connection
         2. Network adapter status
         3. IP configuration
   * - ``-14``
     - Kernel Filter Driver Initialization Failed
     - 1. System permissions
         2. Driver installation
         3. System compatibility

Data Type Errors (``-18`` to ``-21``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Data Type Errors
   :widths: 20 40 40
   
   * - Error Code
     - Data Type Involved
     - Enumeration Reference
   * - ``-18``, ``-19``
     - Network Image Type
     - :c:type:`dtu_type_t`
   * - ``-20``, ``-21``
     - Network Data Type
     - :c:type:`cloudType`

Connection Status Errors (``-22``, ``-32``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Connection Errors
   :widths: 20 40 40
   
   * - Error Code
     - Symptom
     - Solution
   * - ``-22``
     - Heartbeat Timeout
     - 1. Restart camera
         2. Check network stability
   * - ``-32``
     - Complete Disconnection
     - 1. Check power supply
         2. Check physical connections

Parameter Configuration Errors (``-1``, ``-4``, ``-28``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Parameter Errors
   :widths: 20 40 40
   
   * - Error Code
     - Possible Cause
     - Verification Method
   * - ``-1``
     - Parameter Error
     - Check API call parameters
   * - ``-4``
     - Function Not Supported
     - Confirm camera model supports the feature
   * - ``-28``
     - Parameter Retrieval Exception
     - Check SDK version matches firmware

Success Code
------------

In addition to the error codes above, functions return the following upon successful execution:

.. list-table:: Success Code
   :widths: 20 80
   
   * - Return Value
     - Description
   * - ``0``
     - Operation successful, corresponds to ``CAMERA_STATUS_SUCCESS``
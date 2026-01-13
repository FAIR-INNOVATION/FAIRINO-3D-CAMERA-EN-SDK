API Interfaces
==============

.. toctree::
    :maxdepth: 5

Initialize Camera SDK
---------------------
.. c:function:: CameraSdkStatus CameraSdkInit()

   Initialize the SDK.
   
   **Description**
   
   This function needs to be called only once during the entire process runtime. It initializes the global environment of the camera SDK and prepares for subsequent camera operations.
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Notes**
   
   * Must be called before invoking any other SDK functions
   * Should be called only once during the entire process lifecycle
   * Recommended to initialize at program startup
   
   **Example**
   
   .. code-block:: c
   
      CameraSdkStatus status = CameraSdkInit();
      if (status != CAMERA_STATUS_SUCCESS) {
          printf("SDK initialization failed, error code: %d\n", status);
          return -1;
      }
      printf("SDK initialization successful\n");

.. figure:: camera/camera-1.png
    :align: center
    :width: 4in

    SDK initialization output result

Enumerate Camera Devices and Create Device List
-----------------------------------------------
.. c:function:: CameraSdkStatus CameraEnumerateDevice(tSdkCameraDevInfo* pCameraList, int* piNums, uint32_t wTimes)

   Enumerate camera devices and create a device list.
   
   **Description**
   
   Scans all available camera devices in the system, populates device information into the specified array, and returns the actual number of devices found.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraList``
        - [out]
        - Device list array pointer, used to receive device information
      * - ``piNums``
        - [in, out]
        - Device count pointer
          * Input: Number of elements in pCameraList array
          * Output: Actual number of devices found
      * - ``wTimes``
        - [in]
        - Camera enumeration timeout time, unit: seconds
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Usage Example**
   
   .. code-block:: c
   
      #include <stdio.h>
      #include "CameraSdk.h"
      
      int main() {
          CameraSdkStatus status;
          tSdkCameraDevInfo cameraList[10];
          int cameraCount = 10;  // Array capacity
          uint32_t timeout = 5;  // 5 second timeout
          
          // Enumerate devices
          status = CameraEnumerateDevice(cameraList, &cameraCount, timeout);
          
          if (status == CAMERA_STATUS_SUCCESS) {
              printf("Found %d camera devices:\n", cameraCount);
              for (int i = 0; i < cameraCount; i++) {
                  printf("  Device %d: SN=%s, IP=%s, Model=%s\n",
                         i + 1,
                         cameraList[i].cameraSn,
                         cameraList[i].cameraIP,
                         cameraList[i].cameraType);
              }
          } else {
              printf("Device enumeration failed, error code: %d\n", status);
          }
          
          return 0;
      }
   
   **Notes**
   
   1. Must call ``CameraSdkInit()`` to initialize SDK before calling this function
   2. ``pCameraList`` array needs to be pre-allocated with sufficient space
   3. ``piNums`` parameter is both input and output, should be set to array capacity when called
   4. If actual device count exceeds array capacity, only array-sized device information will be filled

Initialize Camera
-----------------
.. c:function:: CameraSdkStatus CameraInit(int iDeviceIndex, CameraHandle* pCameraHandle)

   Initialize camera.
   
   **Description**
   
   Initializes the camera device at the specified index. After successful initialization, other camera-related operation interfaces can be called. This function establishes communication connection with the camera and allocates corresponding resources.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``iDeviceIndex``
        - [in]
        - Camera index number, valid range is 0 to (number of cameras returned by ``CameraEnumerateDeviceEx`` minus 1)
      * - ``pCameraHandle``
        - [out]
        - Camera handle pointer, returns a valid handle for this camera after successful initialization
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   
   **Usage Example**
   
   .. code-block:: c
      :linenos:
      
      #include <stdio.h>
      #include "CameraSdk.h"
      
      int main() {
          CameraSdkStatus status;
          tSdkCameraDevInfo cameraList[10];
          int cameraCount = 10;
          
          // 1. Enumerate devices
          status = CameraEnumerateDevice(cameraList, &cameraCount, 5);
          if (status != CAMERA_STATUS_SUCCESS || cameraCount == 0) {
              printf("No camera devices found\n");
              return -1;
          }
          
          printf("Found %d camera devices\n", cameraCount);
          
          // 2. Initialize first camera
          CameraHandle hCamera = NULL;
          status = CameraInit(0, &hCamera);
          
          if (status == CAMERA_STATUS_SUCCESS) {
              printf("Camera initialization successful, handle: %p\n", (void*)hCamera);
              
              // 3. Use camera...
              
              // 4. Finally release camera
          } else {
              printf("Camera initialization failed, error code: %d\n", status);
          }
          
          return 0;
      }
   
   **Notes**
   
   1. **Calling Order**: Must call ``CameraSdkInit()`` and ``CameraEnumerateDevice()`` before calling this function
   2. **Index Range**: ``iDeviceIndex`` must be within valid device index range (0 ~ device count-1)
   3. **Handle Management**: The ``CameraHandle`` obtained after successful initialization needs to be saved and is required for all subsequent camera operations
   4. **Resource Release**: Must call ``CameraStop()`` to release resources after using the camera
   5. **Multi-Camera Support**: This function can be called multiple times to initialize multiple cameras, each camera has an independent handle
   
   **Related Functions**
   
   * :c:func:`CameraSdkInit` - SDK global initialization
   * :c:func:`CameraEnumerateDevice` - Enumerate camera devices
   * :c:func:`CameraStop` - Deinitialize camera

.. figure:: camera/camera-2.png
    :align: center
    :width: 3in
    
    Enumerate camera and perform camera initialization

Start Camera
------------
.. c:function:: CameraSdkStatus CameraPlay(char pCameraSn[32])

   Put camera into working mode and start receiving image data from camera.
   
   **Description**
   
   Starts the camera with specified SN to enter image acquisition working mode. After calling this function, the camera starts sending image data, which can be received via callback function or active acquisition.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number), maximum 32 characters (including terminator)
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   
   **Usage Example**

.. code-block:: cpp
   :linenos:
   
   #include "CameraApi.h"
   #include <stdio.h>
   #include <string.h>
   
   int main() {
       CameraSdkStatus ret;
       tSdkCameraDevInfo dev_info[10];
       int n_dev_num = 10;
       
       // 1. Initialize SDK
       ret = CameraSdkInit();
       if (ret != CAMERA_STATUS_SUCCESS) {
           perror("Camera SDK Init Error!");
           return -1;
       }
       
       // 2. Enumerate devices
       ret = CameraEnumerateDevice(dev_info, &n_dev_num, 2);
       if (ret != CAMERA_STATUS_SUCCESS) {
           std::cerr << "Camera EnumerateDevice Error!" << std::endl;
           return -1;
       }
       
       if (n_dev_num <= 0) {
           printf("No camera devices found\n");
           return -1;
       }
       
       // 3. Initialize camera (get handle)
       CameraHandle hCamera;
       ret = CameraInit(0, &hCamera);  // Initialize first device
       if (ret != CAMERA_STATUS_SUCCESS) {
           fprintf(stderr, "Camera initialization failed\n");
           return -1;
       }
       
       // 4. Get camera SN (from device info)
       char cameraSn[32];
       strncpy(cameraSn, dev_info[0].cameraSn, sizeof(cameraSn) - 1);
       cameraSn[sizeof(cameraSn) - 1] = '\0';
       
       // 5. Start camera acquisition
       ret = CameraPlay(cameraSn);
       if (ret == CAMERA_STATUS_SUCCESS) {
           fprintf(stderr, "Camera %s --- [Status: Camera opened]\n", cameraSn);
       } else {
           fprintf(stderr, "Camera %s --- [Status: Failed to open camera], error code: %d\n", cameraSn, ret);
           CameraUnInit(hCamera);
           return -1;
       }
       
       // 6. Perform other operations (image acquisition, processing, etc.)
       // ...
       
       // 7. Stop acquisition
       CameraStop(cameraSn); 
.. figure:: camera/camera-3.png
    :align: center
    :width: 3in
    
    Camera acquisition result

Stop Camera
-----------
.. c:function:: CameraSdkStatus CameraStop(char pCameraSn[32])

   Put camera into stopped state.
   
   **Description**
   
   Stops all acquisition activities of the camera with specified SN and puts the camera into stopped state. This function is typically called before camera deinitialization. After stopping, camera parameters cannot be configured.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number), maximum 32 characters (including terminator)
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

Acquire Image Data
------------------
.. c:function:: CameraSdkStatus CameraGetImageBuffer(char pCameraSn[32], tSdkFrameHead* pFrameInfo, char* pbyBuffer, uint32_t wTimes)

   Acquire one frame of 2D or 3D image data.
   
   **Description**
   
   Acquires one frame of image data from the camera with specified SN, including 2D raw image or 3D point cloud data. This function blocks the calling thread until an image is acquired or timeout occurs.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number), maximum 32 characters
      * - ``pFrameInfo``
        - [out]
        - Image frame header information pointer, used to receive image metadata
      * - ``pbyBuffer``
        - [out]
        - Return image data buffer pointer, needs to be pre-allocated with sufficient space before calling
      * - ``wTimes``
        - [in]
        - Image acquisition timeout time, unit milliseconds. Returns timeout error if no image is acquired within wTimes
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

Acquire Point Cloud Data
------------------------
.. c:function:: CameraSdkStatus CameraGetPointCloudData(char pCameraSn[32], tSdkSendPCDParam pPCDParam)

   Acquire one frame of point cloud data from thread.
   
   **Description**
   
   Triggers the camera with specified SN to acquire one frame of point cloud data (3D data). This function sends acquisition command to the camera, then the camera starts the acquisition process. Point cloud data is returned via callback function or ``CameraGetImageBuffer``.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number), maximum 32 characters
      * - ``pPCDParam``
        - [in]
        - Point cloud data configuration parameter structure, sets acquisition mode and parameters
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

Multi-threaded Data Acquisition
-------------------------------

Overview
--------

``uiDisplayThread`` is an independent image acquisition and display thread function responsible for acquiring 2D/3D image data from camera and processing/displaying it.

Function Prototype
------------------

.. code-block:: cpp
   
   void* uiDisplayThread(void* lpParam);

Parameter Description
---------------------

.. list-table:: Parameter Description
   :widths: 25 75
   :header-rows: 1
   
   * - Parameter
     - Description
   * - ``lpParam``
     - Thread parameter pointer, points to :c:type:`tSdkCameraDevInfo` structure containing camera device information

Return Value
------------

* Success: Returns ``NULL``
* Failure: Function doesn't explicitly return failure but is controlled by global variable ``running``

Function Description
--------------------

This function implements the following functions:

1. **Initialize Resources**: Allocate image buffers according to camera resolution
2. **Continuous Acquisition**: Continuously acquire image data from camera
3. **Data Classification**: Process 2D images or 3D point cloud data according to frame type
4. **Display/Save**: Display 2D images or save 3D point cloud data to files

Workflow
--------

.. code-block:: text
   
   Thread starts
     ↓
   Allocate buffers (Xw, Yw, Zw, pGrayBuffer, etc.)
     ↓
   while(running) loop
     ↓
   CameraGetImageBuffer() acquire image
     ↓
   Determine FrameInfo.iType
     ├─ DTU_FRAME_RAW (2D image) → Display left/right camera images
     └─ DTU_FRAME_MAP (3D point cloud) → Save point cloud data to file
     ↓
   Wait for next frame
     ↓
   running = false → Release resources → Thread exits

Complete Code
-------------

.. code-block:: cpp
   :linenos:
   :emphasize-lines: 30-35, 44-48, 60-65
   
   void* uiDisplayThread(void* lpParam) {
       tSdkCameraDevInfo* devInfo = (tSdkCameraDevInfo*)lpParam;
   
       int nImage_Width_IR = devInfo->irCameraResWidth;
    int nImage_Height_IR = devInfo->irCameraResHeight;
    int nImage_Width_RGB = devInfo->rgbCameraResWidth;
    int nImage_Height_RGB = devInfo->rgbCameraResHeight;
       int nImage_Size_IR = nImage_Width_IR * nImage_Height_IR;
       int nImage_Size_RGB = nImage_Width_RGB * nImage_Height_RGB;
       
    float *Xw = new float[nImage_Size_IR];
    float *Yw = new float[nImage_Size_IR];
    float *Zw = new float[nImage_Size_IR];
    char *pGrayBuffer = new char[nImage_Size_IR];
   
       tSdkFrameHead FrameInfo;
       char* pbyBuffer = new char[nImage_Size_IR * 16];
   
       uint8_t* T_L = new uint8_t[nImage_Size_IR];
       uint8_t* T_R = new uint8_t[nImage_Height_IR];
   
       while (running) {
           // Acquire image data, timeout 1000ms
           int ii = CameraGetImageBuffer(devInfo->cameraSn, &FrameInfo, pbyBuffer, 1000);
   
           if (FrameInfo.iType == DTU_FRAME_RAW) {
            continue;  // Skip 2D image processing (in this example)
               // Receive and display 2D images
               memset(T_R, 0, nImage_Size_IR);
               memcpy(T_R, pbyBuffer, nImage_Size_IR);
               cv::Mat mat_RightCamera(nImage_Height_IR, nImage_Width_IR, CV_8UC1, T_R);
   
               memset(T_L, 0, nImage_Size_IR);
               memcpy(T_L, pbyBuffer + nImage_Size_IR, nImage_Size_IR);
               cv::Mat mat_LeftCamera(nImage_Height_IR, nImage_Width_IR, CV_8UC1, T_L);
   
               cv::namedWindow("Right Camera_1", 0);
               cv::resizeWindow("Right Camera_1", nImage_Width_IR / 2, nImage_Height_IR / 2);
               cv::imshow("Right Camera_1", mat_RightCamera);
   
               cv::namedWindow("Left Camera_1", 0);
               cv::resizeWindow("Left Camera_1", nImage_Width_IR / 2, nImage_Height_IR / 2);
               cv::imshow("Left Camera_1", mat_LeftCamera);
   
               if (FrameInfo.iRGBWidth != 0) {
                   uint8_t * mjpg = (uint8_t*)(pbyBuffer + 13 * nImage_Size_IR);
                   std::vector<uchar> copiedVec(mjpg, mjpg + FrameInfo.iDataSize -  13 * nImage_Size_IR);
                   cv::Mat jpg_frame = cv::imdecode(copiedVec, cv::IMREAD_COLOR);
                   cv::resize(jpg_frame, jpg_frame, cv::Size(), 0.5, 0.5); // Scale down to half size
                   cv::imshow("RGB_MJPG", jpg_frame);
               }
               cv::waitKey(1);
           } else if (FrameInfo.iType == DTU_FRAME_MAP) {
   
               // Receive and display 3D images
               if (FrameInfo.iPointCloudDataType == CLOUD) {
                   char file_name[256];
                   FILE* pFile;
                   // Point cloud X information
                   memcpy(Xw, pbyBuffer, nImage_Size_IR * sizeof(float));
                   // Point cloud Y information
                   memcpy(Yw, pbyBuffer + nImage_Size_IR * 4 * 1, nImage_Size_IR * sizeof(float));
                   // Point cloud Z information
                   memcpy(Zw, pbyBuffer + nImage_Size_IR * 4 * 2, nImage_Size_IR * sizeof(float));
   
                   // Rectified left camera image (2D)
                   memcpy(pGrayBuffer, pbyBuffer + nImage_Size_IR * 4 * 3, nImage_Size_IR);
   
                FILE* FilePosition_Xw = fopen("./Xw.txt", "w");
                   for (int i = 0; i < nImage_Size_IR; i++)
                   {
                           fprintf(FilePosition_Xw, "%f\n", Xw[i]);
                   }
                fclose(FilePosition_Xw);
   
                   FILE* FilePosition_Yw = fopen("./Yw.txt", "w");
                for (int i = 0; i < nImage_Size_IR; i++)
                {
                    fprintf(FilePosition_Yw, "%f\n", Yw[i]);
                }
                fclose(FilePosition_Yw);
                FILE* FilePosition_Zw = fopen("./Zw.txt", "w");
                   int count = 0;
                for (int i = 0; i < nImage_Size_IR; i++)
                {
                    fprintf(FilePosition_Zw, "%f\n", Zw[i]);
                       if(Zw[i] != 0 )
                           count++;
                }
                fclose(FilePosition_Zw);
   
                   cv::Mat image(nImage_Height_IR, nImage_Width_IR, CV_8UC(1), pGrayBuffer);
                cv::imwrite("./pGrayBuffer.bmp", image);
   
                   m_bNewPCDbuffer = 1;
                   fprintf(stderr, "Save Once Cloud Ok!, Useful Cloud Count: %d\n", count);
               }
           }
       }
       return NULL; 
   }

Parameter Settings and Acquisition
----------------------------------
Overview
--------

Built-in camera parameters such as exposure parameters, shooting modes, etc., support two configuration methods:

1. **Direct Structure Modification**: Define parameter structure and directly modify member values
2. **Using API Functions**: Call dedicated set/get functions

Both methods can be used separately or combined.

Set Exposure Parameters / Get Exposure Parameters
-------------------------------------------------
CameraSetExposureTime
---------------------

.. c:function:: CameraSdkStatus CameraSetExposureTime(char pCameraSn[32], int nCameraID, int iExposureTime)

   Set camera exposure time.
   
   **Description**
   
   Sets the exposure time for specified camera lens, unit: microseconds (μs). This function directly modifies the exposure time values in the corresponding :c:type:`tSdkSendPCDParam` structure.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``nCameraID``
        - [in]
        - Camera lens ID:
          
          * 0: Left monochrome lens (corresponds to iIRExposureTimes1)
          * 1: Right monochrome lens (corresponds to iIRExposureTimes2)
          * 2: Color lens (corresponds to iRGBExposureTimes)
      * - ``iExposureTime``
        - [in]
        - Exposure time, unit microseconds (μs)
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Notes**
   
   1. **Parameter Correspondence**:
      
      .. list-table:: Lens ID and Parameter Correspondence
         :widths: 20 30 50
         
         * - Lens ID
           - Structure Parameter
           - Description
         * - 0 (Left monochrome)
           - ``iIRExposureTimes1``
           - First exposure time
         * - 1 (Right monochrome)
           - ``iIRExposureTimes2``
           - Second exposure time
         * - 2 (Color)
           - ``iRGBExposureTimes``
           - RGB camera exposure time
   
   2. **Effective Time**: Requires re-acquisition of images to take effect after setting
   3. **Unit**: Microseconds (μs), 1 millisecond = 1000 microseconds

Trigger Mode Setting and Acquisition Functions
---------------------------------------------

CameraSetTriggerMode
--------------------

.. c:function:: CameraSdkStatus CameraSetTriggerMode(char pCameraSn[32], int iModeSel)

   Set camera trigger mode.
   
   **Description**
   
   Sets the image acquisition trigger mode for specified camera, controlling how image acquisition is initiated.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``iModeSel``
        - [in]
        - Mode selection index:
          
          * 0: Continuous acquisition mode
          * 1: Triggered acquisition mode
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

Set Camera Protective Cover Open or Close
-----------------------------------------

CameraSetDoorState
------------------

.. c:function:: CameraSdkStatus CameraSetDoorState(char pCameraSn[32], int iDoorState, int &call_size, char *recv_bytes)

   Set camera front cover state.
   
   **Description**
   
   Controls the opening or closing operation of the camera's front cover (protective cover). Some camera models come with controllable front covers for lens protection or adapting to different measurement scenarios.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``iDoorState``
        - [in]
        - Front cover state:
          
          * 0: Close front cover
          * 1: Normal angle open
          * 2: Wide angle open
      * - ``call_size``
        - [in, out]
        - Communication data size
          
          * Input: Size of ``recv_bytes`` buffer
          * Output: Actual number of data bytes received
      * - ``recv_bytes``
        - [out]
        - Receive buffer pointer, used to receive device response data
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

Enable RGB Data Stream
----------------------
RGB Camera Data Stream Control Function
---------------------------------------

CameraRGBSwitch
---------------

.. c:function:: CameraSdkStatus CameraRGBSwitch(char pCameraSn[32], int iRGBSwitch)

   Set whether RGB camera outputs data stream.
   
   **Description**
   
   Controls the RGB color camera data stream output switch for specified camera. When RGB camera is closed, no color image data is output, which can reduce data transmission volume and improve processing speed.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``iRGBSwitch``
        - [in]
        - RGB camera switch state:
          
          * 0: Disable RGB data stream
          * 1: Enable RGB data stream
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Function Description**
   
   1. **Disable RGB Data Stream** (iRGBSwitch = 0)
      
      * No color image data output
      * Reduced data transmission bandwidth
      * Improved processing speed (only IR data processing)
      * Suitable for scenarios requiring only depth/point cloud data
   
   2. **Enable RGB Data Stream** (iRGBSwitch = 1)
      
      * Color image data output
      * Can be used for colored point clouds, texture mapping
      * Requires camera to support RGB function
      * Increased data transmission volume
   
   **Notes**
   
   1. **Hardware Requirement**: Only models with RGB camera support this function
   2. **Calling Time**: Recommended to set after camera initialization, before starting acquisition
   3. **Parameter Correspondence**: This setting affects the ``iRGBResolutionMode`` parameter in :c:type:`tSdkSendPCDParam`
   4. **Data Acquisition**: RGB data is acquired via ``CameraGetImageBuffer`` or callback function

Set Camera IP
-------------
Camera IP Address Setting Function
----------------------------------

CameraSetIP
-----------

.. c:function:: CameraSdkStatus CameraSetIP(char pCameraSn[32], char* pCameraIP)

   Set camera IP address.
   
   **Description**
   
   Modifies the IP address of specified network camera. This function changes the camera's network configuration, making the camera communicate using the new IP address.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``pCameraIP``
        - [in]
        - Camera new IP address string (e.g., "192.168.1.100")
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Notes**
   
   1. **Connection Requirement**: Must be able to establish network connection with camera
   2. **IP Format**: Must be valid IPv4 address format
   3. **Network Conflict**: Ensure new IP address is unique in local network and doesn't conflict with other devices
   4. **Reconnection**: Need to reconnect using new IP after setting IP
   5. **Permission Requirement**: May require administrator privileges

Get Camera Intrinsic Parameters
-------------------------------
Camera Calibration Parameter Acquisition Function
------------------------------------------------

CameraGetCalibrationData
------------------------

.. c:function:: CameraSdkStatus CameraGetCalibrationData(char pCameraSn[32], tSdkCameraCalibration_IR_IR* CameraCalibration_IR_IR, tSdkCameraCalibration_IR_RGB* CameraCalibration_IR_RGB)

   Get calibration intrinsic parameters
   
   **Description**
   
   Acquires calibration parameters of specified camera, including binocular IR camera calibration parameters and IR-RGB camera calibration parameters.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 30 20 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``CameraCalibration_IR_IR``
        - [out]
        - Binocular IR camera calibration parameter structure pointer, receives calibration data of left/right IR cameras
      * - ``CameraCalibration_IR_RGB``
        - [out]
        - IR-RGB camera calibration parameter structure pointer, receives calibration data between IR camera and RGB camera
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

.. figure:: camera/camera-4.png
    :align: center
    :width: 3in
    
    Get camera intrinsic parameters

Set Structured Light Data Processing Parameters
-----------------------------------------------
Structured Light Calculation Parameter Setting Function
-------------------------------------------------------

CameraSetComputeParameter
-------------------------

.. c:function:: CameraSdkStatus CameraSetComputeParameter(char pCameraSn[32], tSdkCameraComputeParameterInfo pComputeParam)

   Set structured light calculation parameters to handle false data issues.
   
   **Description**
   
   Configures point cloud calculation parameters of camera to optimize structured light 3D reconstruction quality, particularly for filtering and processing false data (noise, mismatched points).
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 30 20 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``pComputeParam``
        - [in]
        - Calculation parameter structure, contains various filtering and optimization parameters
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions

.. figure:: camera/camera-5.png
    :align: center
    :width: 3in
    
    Structured light data processing

Structured Light Calculation Parameter Acquisition Function
----------------------------------------------------------

CameraGetComputeParameter
-------------------------

.. c:function:: CameraSdkStatus CameraGetComputeParameter(char pCameraSn[32], tSdkCameraComputeParameterInfo* ComputeParam)

   Get line scan mode calculation parameters to handle false data issues.
   
   **Description**
   
   Queries current structured light calculation parameters set for specified camera. These parameters control point cloud calculation quality, particularly for filtering and processing false data (noise, mismatched points).
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 30 20 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``ComputeParam``
        - [out]
        - Calculation parameter structure pointer, used to receive current calculation parameters
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Acquired Parameter Description**
   
   The returned :c:type:`tSdkCameraComputeParameterInfo` structure contains the following parameters:
   
   .. list-table:: Calculation Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter
        - Type
        - Function Description
      * - ``iThr_num``
        - ``uint32_t``
        - Noise filtering threshold, smaller values filter less noise
      * - ``fMaxDiff``
        - ``float``
        - Maximum difference threshold, larger values filter less noise
      * - ``fPhase_LR``
        - ``float``
        - Phase matching parameter, larger values yield more valid data
      * - ``fModulation``
        - ``float``
        - Modulation parameter, smaller values yield more valid data
      * - ``iDepthMin``
        - ``uint32_t``
        - Minimum measurement distance (millimeters)
      * - ``iDepthMax``
        - ``uint32_t``
        - Maximum measurement distance (millimeters)

Get and Set Line Scan Mode Parameter Settings
---------------------------------------------
Line Scan Mode Calculation Parameter Setting and Acquisition Functions
----------------------------------------------------------------------

CameraSetLineComputeParameter
-----------------------------

.. c:function:: CameraSdkStatus CameraSetLineComputeParameter(char pCameraSn[32], tSdkCameraLineComputeParameterInfo pComputeParam)

   Set line scan mode calculation parameters to handle false data issues.
   
   **Description**
   
   Configures point cloud calculation parameters in line laser scanning mode to optimize line laser 3D reconstruction quality, filtering and processing false data (noise, mismatched points).
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 30 20 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``pComputeParam``
        - [in]
        - Line scan calculation parameter structure, contains various filtering and optimization parameters
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
   
   **Parameter Details**
   
   .. list-table:: Line Scan Calculation Parameter Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Parameter
        - Type
        - Function Description
      * - ``Thr_num_line``
        - ``uint32_t``
        - Line scan noise filtering threshold (count), default 100, larger values provide stronger filtering
      * - ``maxDiff_line``
        - ``float``
        - Line scan noise filtering threshold, default 0.5, smaller values provide stronger filtering
      * - ``threshold``
        - ``float``
        - Disparity map filtering threshold, default 1
      * - ``img_thrd``
        - ``float``
        - Center line extraction threshold (0~255), larger values yield smaller center lines, faster calculation speed
      * - ``default_prj_light``
        - ``uint32_t``
        - Determine if belongs to brightness-enhanced version (only for 130J version)
      * - ``default_gain_set``
        - ``uint32_t``
        - Determine if camera gain configuration has changed (version ≥2.2.07 indicates gain change)
      * - ``kernel_Gauss_mode``
        - ``int``
        - Smoothing coefficient (options: 0, 1, 2)
      * - ``filter_num``
        - ``int``
        - False data filtering parameter, default 3, range [0, 10]

CameraGetLineComputeParameter
-----------------------------

.. c:function:: CameraSdkStatus CameraGetLineComputeParameter(char pCameraSn[32], tSdkCameraLineComputeParameterInfo* ComputeParam)

   Get line scan mode calculation parameters to handle false data issues.
   
   **Description**
   
   Queries current line scan calculation parameters set for specified camera.
   
   **Parameters**
   
   .. list-table:: Parameter Description
      :widths: 30 20 50
      :header-rows: 1
      
      * - Parameter Name
        - Direction
        - Description
      * - ``pCameraSn``
        - [in]
        - Camera SN (serial number)
      * - ``ComputeParam``
        - [out]
        - Line scan calculation parameter structure pointer, used to receive current parameter settings
   
   **Return Value**
   
   :Success: Returns ``CAMERA_STATUS_SUCCESS`` (0)
   :Failure: Returns non-zero error code, please refer to CameraStatus.h for error code definitions
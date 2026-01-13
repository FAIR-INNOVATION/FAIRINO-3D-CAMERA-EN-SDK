Data Structures
===============

.. toctree:: 
    :maxdepth: 5

Camera Device Information
-------------------------
.. c:type:: tSdkCameraDevInfo

   Camera device information structure.
   
   .. list-table:: Structure Member Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Description
      * - ``cameraSn``
        - ``char[32]``
        - Camera serial number
      * - ``cameraIP``
        - ``char[128]``
        - Camera IP address
      * - ``cameraType``
        - ``char[32]``
        - Camera model
      * - ``cameraFWVersion``
        - ``char[32]``
        - Camera firmware version
      * - ``irCameraResWidth``
        - ``int``
        - IR image width (pixels)
      * - ``irCameraResHeight``
        - ``int``
        - IR image height (pixels)
      * - ``rgbCameraResWidth``
        - ``int``
        - RGB image width (pixels)
      * - ``rgbCameraResHeight``
        - ``int``
        - RGB image height (pixels)
      * - ``projectorBatch``
        - ``char[32]``
        - Projector module batch
      * - ``projectorFWVersion``
        - ``char[32]``
        - Projector firmware version
      * - ``projectorFlashState``
        - ``int``
        - Projector flash memory state

Image Frame Header Information
------------------------------
.. c:type:: tSdkFrameHead

   Frame header information structure, containing metadata information for image data.
   
   .. list-table:: Structure Member Description
      :widths: 25 20 55
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Description
      * - ``iStx``
        - ``uint16_t``
        - Start identifier
      * - ``iType``
        - ``uint16_t``
        - Major type (2D image, 3D image)
      * - ``iPointCloudDataType``
        - ``uint32_t``
        - Point cloud data type (X, Y, Z)
      * - ``iTimeStamp``
        - ``uint64_t``
        - Acquisition timestamp of this frame
      * - ``iDataSize``
        - ``uint32_t``
        - Total data size (bytes)
      * - ``iPointDataSize``
        - ``uint32_t``
        - Point cloud data size (bytes)
      * - ``iIRWidth``
        - ``uint32_t``
        - IR image width (pixels)
      * - ``iIRHeight``
        - ``uint32_t``
        - IR image height (pixels)
      * - ``iIRDataSize``
        - ``uint32_t``
        - IR image data size (bytes)
      * - ``iRGBWidth``
        - ``uint32_t``
        - RGB image width (pixels)
      * - ``iRGBHeight``
        - ``uint32_t``
        - RGB image height (pixels)
      * - ``iRGBDataSize``
        - ``uint32_t``
        - RGB image data size (bytes)
      * - ``fCameraTemperature``
        - ``float``
        - Camera temperature (degrees Celsius)

Data Acquisition Settings Parameters
------------------------------------
.. c:type:: tSdkSendPCDParam

   Point cloud data sending parameter configuration structure.
   
   .. list-table:: Structure Member Description
      :widths: 25 20 30 25
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Description
        - Default Value/Range
      * - ``iTriggerMode``
        - ``uint16_t``
        - Trigger mode
        - 0: Single trigger, 1: Continuous trigger
      * - ``iProjectorMode``
        - ``uint16_t``
        - Projector mode
        - 0: Fast, 1: Normal, 2: High precision
      * - ``iSerialNumber``
        - ``uint8_t``
        - Serial port number
        - 0
      * - ``iIRResolutionMode``
        - ``uint8_t``
        - Binocular camera resolution mode
        - 0: Default resolution, 1: Binning
      * - ``iRGBResolutionMode``
        - ``uint8_t``
        - Color resolution mode
        - 0: No RGB output, 1: Default, 2: Binning
      * - ``iIRExposureCount``
        - ``uint8_t``
        - Binocular camera exposure count
        - 1~3
      * - ``iIRExposureTimes1``
        - ``uint32_t``
        - Binocular camera exposure time 1
        - 10000μs
      * - ``iIRExposureTimes2``
        - ``uint32_t``
        - Binocular camera exposure time 2
        - 10000μs
      * - ``iIRExposureTimes3``
        - ``uint32_t``
        - Binocular camera exposure time 3
        - 10000μs
      * - ``fIRGain``
        - ``float``
        - Binocular camera gain
        - 2.5x
      * - ``iRGBExposureTimes``
        - ``uint32_t``
        - Color camera exposure time
        - 10000μs
      * - ``fRGBGain``
        - ``float``
        - Color camera gain
        - 2.5x
      * - ``iIRWhiteExpo``
        - ``uint32_t``
        - Binocular camera grayscale image exposure
        - 2500μs
      * - ``fIRWhiteGain``
        - ``float``
        - Binocular camera grayscale image gain
        - 2.5x

Structured Light Mode Data Processing Parameters
------------------------------------------------
.. c:type:: tSdkCameraComputeParameterInfo

   Camera calculation parameter information structure, used to configure various parameters for point cloud calculation.
   
   .. list-table:: Structure Member Description
      :widths: 25 20 25 30
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Default Value
        - Description
      * - ``iThr_num``
        - ``uint32_t``
        - 180
        - Noise filtering threshold, smaller values filter less noise (minimum 0)
      * - ``fMaxDiff``
        - ``float``
        - 0.5
        - Maximum difference threshold, larger values filter less noise (minimum 0)
      * - ``fPhase_LR``
        - ``float``
        - 0.5
        - Phase matching parameter, larger values yield more valid data (minimum 0)
      * - ``fModulation``
        - ``float``
        - 0
        - Modulation parameter, smaller values yield more valid data (minimum 0)
      * - ``iDepthMin``
        - ``uint32_t``
        - 300
        - Minimum measurement distance (unit: millimeters)
      * - ``iDepthMax``
        - ``uint32_t``
        - 1300
        - Maximum measurement distance (unit: millimeters)

Line Scan Mode Data Processing Parameters
-----------------------------------------
.. c:type:: tSdkCameraLineComputeParameterInfo

   Camera line calculation parameter information structure, used to configure various parameters for line laser calculation.
   
   .. list-table:: Structure Member Description
      :widths: 28 18 18 36
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Default Value
        - Description
      * - ``Thr_num_line``
        - ``uint32_t``
        - 200
        - Line scan noise filtering threshold (count), default 100
      * - ``maxDiff_line``
        - ``float``
        - 0.25
        - Line scan noise filtering threshold, default 0.5
      * - ``threshold``
        - ``float``
        - 1
        - Disparity map filtering threshold, default 1
      * - ``img_thrd``
        - ``float``
        - 20
        - Center line extraction threshold (0~255), larger values yield smaller center lines, faster calculation speed
      * - ``default_prj_light``
        - ``uint32_t``
        - -
        - Determine if belongs to brightness-enhanced version (only for 130J version)
      * - ``default_gain_set``
        - ``uint32_t``
        - -
        - Determine if camera gain configuration has changed (version ≥2.2.07 indicates gain change, only for 130J version)
      * - ``kernel_Gauss_mode``
        - ``int``
        - -
        - Smoothing coefficient (options: 0, 1, 2)
      * - ``filter_num``
        - ``int``
        - 3
        - False data filtering parameter, range [0, 10]

Binocular Calibration Intrinsic and Extrinsic Parameters
--------------------------------------------------------
Intrinsic Parameters Without RGB Mode
--------------------------------------
.. c:type:: tSdkCameraCalibration_IR_IR

   Binocular IR camera calibration parameter structure, containing intrinsic parameters, extrinsic parameters, and distortion parameters for left and right cameras.
   
   .. list-table:: Calibration Parameter Structure
      :widths: 25 20 55
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Description
      * - **Left Camera Parameters**
        - 
        - 
      * - ``fc_left``
        - ``double[2]``
        - Left camera focal length (fx, fy)
      * - ``cc_left``
        - ``double[2]``
        - Left camera principal point coordinates (cx, cy)
      * - ``kc_left``
        - ``double[5]``
        - Left camera distortion coefficients [k1, k2, p1, p2, k3]
      * - ``R_left``
        - ``double[9]``
        - Left camera rotation matrix (3×3)
      * - ``alpha_c_left``
        - ``double``
        - Left camera skew coefficient
      * - **Right Camera Parameters**
        - 
        - 
      * - ``fc_right``
        - ``double[2]``
        - Right camera focal length (fx, fy)
      * - ``cc_right``
        - ``double[2]``
        - Right camera principal point coordinates (cx, cy)
      * - ``kc_right``
        - ``double[5]``
        - Right camera distortion coefficients [k1, k2, p1, p2, k3]
      * - ``R_right``
        - ``double[9]``
        - Right camera rotation matrix (3×3)
      * - ``alpha_c_right``
        - ``double``
        - Right camera skew coefficient
      * - **New Calibration Parameters**
        - 
        - 
      * - ``fc_left_new``
        - ``double[2]``
        - Left camera new focal length (fx, fy)
      * - ``cc_left_new``
        - ``double[2]``
        - Left camera new principal point coordinates (cx, cy)
      * - ``fc_right_new``
        - ``double[2]``
        - Right camera new focal length (fx, fy)
      * - ``cc_right_new``
        - ``double[2]``
        - Right camera new principal point coordinates (cx, cy)
      * - ``kc_new``
        - ``double[2]``
        - New distortion coefficients [k1, k2]
      * - ``R_new``
        - ``double[9]``
        - New rotation matrix (3×3)
      * - ``T_new``
        - ``double[3]``
        - New translation vector (tx, ty, tz)

RGB Camera Intrinsic Parameters
-------------------------------
.. c:type:: tSdkCameraCalibration_IR_RGB

   IR and RGB camera joint calibration parameter structure, containing intrinsic and extrinsic parameters for IR and RGB cameras.
   
   .. list-table:: IR-RGB Camera Calibration Parameters
      :widths: 25 20 20 35
      :header-rows: 1
      
      * - Member Name
        - Data Type
        - Array Size
        - Description
      * - **IR Camera Parameters**
        - 
        - 
        - 
      * - ``fc_left``
        - ``double``
        - 2
        - IR camera focal length (fx, fy)
      * - ``cc_left``
        - ``double``
        - 2
        - IR camera principal point coordinates (cx, cy)
      * - ``kc_left``
        - ``double``
        - 5
        - IR camera distortion coefficients [k1, k2, p1, p2, k3]
      * - ``R_left``
        - ``double``
        - 9
        - IR camera rotation matrix (3×3)
      * - ``alpha_c_left``
        - ``double``
        - 1
        - IR camera skew coefficient
      * - **RGB Camera Parameters**
        - 
        - 
        - 
      * - ``fc_right``
        - ``double``
        - 2
        - RGB camera focal length (fx, fy)
      * - ``cc_right``
        - ``double``
        - 2
        - RGB camera principal point coordinates (cx, cy)
      * - ``kc_right``
        - ``double``
        - 5
        - RGB camera distortion coefficients [k1, k2, p1, p2, k3]
      * - ``R_right``
        - ``double``
        - 9
        - RGB camera rotation matrix (3×3)
      * - ``alpha_c_right``
        - ``double``
        - 1
        - RGB camera skew coefficient
      * - **New Calibration Parameters**
        - 
        - 
        - 
      * - ``fc_left_new``
        - ``double``
        - 2
        - IR camera new focal length (fx, fy)
      * - ``cc_left_new``
        - ``double``
        - 2
        - IR camera new principal point coordinates (cx, cy)
      * - ``fc_right_new``
        - ``double``
        - 2
        - RGB camera new focal length (fx, fy)
      * - ``cc_right_new``
        - ``double``
        - 2
        - RGB camera new principal point coordinates (cx, cy)
      * - ``kc_new``
        - ``double``
        - 2
        - New distortion coefficients [k1, k2]
      * - ``R_new``
        - ``double``
        - 9
        - IR to RGB rotation matrix (3×3)
      * - ``T_new``
        - ``double``
        - 3
        - IR to RGB translation vector (tx, ty, tz)

Image Types
-----------
.. c:type:: cloudType

   Point cloud data type enumeration, defining different data output formats.
   
   .. list-table:: Enumeration Value Description
      :widths: 25 75
      :header-rows: 1
      
      * - Enumeration Value
        - Description
      * - ``CLOUD``
        - Complete point cloud data (contains X, Y, Z coordinates)
      * - ``CLOUD_X``
        - X coordinate data only
      * - ``CLOUD_Y``
        - Y coordinate data only
      * - ``CLOUD_Z``
        - Z coordinate data only
      * - ``GRAY``
        - Grayscale image data
      * - ``GRAY_RAW``
        - Raw grayscale image data

Data Types
----------
.. c:type:: dtu_type_t

   DTU (Data Transmission Unit) data type enumeration.
   
   .. list-table:: Enumeration Value Description
      :widths: 25 25 50
      :header-rows: 1
      
      * - Enumeration Value
        - Hexadecimal Value
        - Description
      * - ``DTU_FRAME_RAW``
        - ``0x0000``
        - Raw image (original data)
      * - ``DTU_FRAME_MAP``
        - ``0x0001``
        - Point cloud map
      * - ``DTU_FRAME_NULL``
        - ``0x0002``
        - Null frame type
      * - ``DTU_FRAME_STRIPE``
        - ``0x0003``
        - Stripe image
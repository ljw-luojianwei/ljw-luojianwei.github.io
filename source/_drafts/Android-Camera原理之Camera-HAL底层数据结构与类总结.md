---
title: Android Camera原理之Camera HAL底层数据结构
tags:
  - Camera
date: 2021-05-31 14:18:47
---


### hardware/libhardware/include/hardware/hardware.h

#### hw_module_t : hw_module_t

```c
typedef struct hw_module_t {
    uint32_t tag;
    uint16_t module_api_version;
#define version_major module_api_version
    uint16_t hal_api_version;
#define version_minor hal_api_version
    const char *id;
    const char *name;
    const char *author;
    struct hw_module_methods_t* methods;
    void* dso;
#ifdef __LP64__
    uint64_t reserved[32-7];
#else
    uint32_t reserved[32-7];
#endif
} hw_module_t;
```

#### hw_module_methods_t : hw_module_methods_t

```c 
typedef struct hw_module_methods_t {
    int (*open)(const struct hw_module_t* module, const char* id,
            struct hw_device_t** device);
} hw_module_methods_t;
```

#### hw_device_t : hw_device_t

```c
typedef struct hw_device_t {
    uint32_t tag;
    uint32_t version;
    struct hw_module_t* module;
#ifdef __LP64__
    uint64_t reserved[12];
#else
    uint32_t reserved[12];
#endif
    int (*close)(struct hw_device_t* device);
} hw_device_t;
```

### hardware/libhardware/include/hardware/camera_common.h

#### camera_info_t : camera_info

```c
typedef struct camera_info {
    int facing;
    int orientation;
    uint32_t device_version;
    const camera_metadata_t *static_camera_characteristics;
    int resource_cost;
    char** conflicting_devices;
    size_t conflicting_devices_length;
} camera_info_t;
```

#### camera_device_status_t : camera_device_status

```c
typedef enum camera_device_status {
    CAMERA_DEVICE_STATUS_NOT_PRESENT = 0,
    CAMERA_DEVICE_STATUS_PRESENT = 1,
    CAMERA_DEVICE_STATUS_ENUMERATING = 2,
} camera_device_status_t;
```

#### torch_mode_status_t : torch_mode_status

```c
typedef enum torch_mode_status {
    TORCH_MODE_STATUS_NOT_AVAILABLE = 0,
    TORCH_MODE_STATUS_AVAILABLE_OFF = 1,
    TORCH_MODE_STATUS_AVAILABLE_ON = 2,
} torch_mode_status_t;
```

#### camera_module_callbacks_t : camera_module_callbacks

```c
typedef struct camera_module_callbacks {
    void (*camera_device_status_change)(const struct camera_module_callbacks*,
            int camera_id,
            int new_status);

    void (*torch_mode_status_change)(const struct camera_module_callbacks*,
            const char* camera_id,
            int new_status);
} camera_module_callbacks_t;
```

#### camera_stream_t:camera_stream 

```c
typedef struct camera_stream {
    int stream_type;
    uint32_t width;
    uint32_t height;
    int format;
    uint32_t usage;
    android_dataspace_t data_space;
    int rotation;
    const char* physical_camera_id; 
} camera_stream_t;
```

#### camera_stream_combination_t:camera_stream_combination

```c
typedef struct camera_stream_combination {
    uint32_t num_streams;
    camera_stream_t *streams;
    uint32_t operation_mode;
} camera_stream_combination_t;
```

#### device_state_t:device_state

```c
typedef enum device_state {
    NORMAL = 0,
    BACK_COVERED = 1 << 0,
    FRONT_COVERED = 1 << 1,
    FOLDED = 1 << 2,
    VENDOR_STATE_START = 1LL << 32
} device_state_t;
```

####  camera_module_t : camera_module

```c
typedef struct camera_module {
    hw_module_t common;
    int (*get_number_of_cameras)(void);
    int (*get_camera_info)(int camera_id, struct camera_info *info);
    int (*set_callbacks)(const camera_module_callbacks_t *callbacks);
    void (*get_vendor_tag_ops)(vendor_tag_ops_t* ops);
    int (*open_legacy)(const struct hw_module_t* module, const char* id,
            uint32_t halVersion, struct hw_device_t** device);
    int (*set_torch_mode)(const char* camera_id, bool enabled);
    int (*init)();
    int (*get_physical_camera_info)(int physical_camera_id,
            camera_metadata_t **static_metadata);
    int (*is_stream_combination_supported)(int camera_id,
            const camera_stream_combination_t *streams);
    void (*notify_device_state_change)(uint64_t deviceState);
    void* reserved[5];
} camera_module_t;
```

### 3.hardware/libhardware/include/hardware/camera3.h

#### camera3_device_t : camera3_device

```c
typedef struct camera3_device {
    hw_device_t common;
    camera3_device_ops_t *ops;
    void *priv;
} camera3_device_t;
```

#### camera3_device_ops_t : camera3_device_ops

```c
typedef struct camera3_device_ops {
    int (*initialize)(const struct camera3_device *,
            const camera3_callback_ops_t *callback_ops);
    int (*configure_streams)(const struct camera3_device *,
            camera3_stream_configuration_t *stream_list);
    int (*register_stream_buffers)(const struct camera3_device *,
            const camera3_stream_buffer_set_t *buffer_set);
    const camera_metadata_t* (*construct_default_request_settings)(
            const struct camera3_device *,
            int type);
    int (*process_capture_request)(const struct camera3_device *,
            camera3_capture_request_t *request);
    void (*get_metadata_vendor_tag_ops)(const struct camera3_device*,
            vendor_tag_query_ops_t* ops);
    void (*dump)(const struct camera3_device *, int fd);
    int (*flush)(const struct camera3_device *);
    void *reserved[8];
} camera3_device_ops_t;
```

#### camera3_callback_ops_t : camera3_callback_ops

```c
typedef struct camera3_callback_ops {
    void (*process_capture_result)(const struct camera3_callback_ops *,
            const camera3_capture_result_t *result);
    void (*notify)(const struct camera3_callback_ops *,
            const camera3_notify_msg_t *msg);
    camera3_buffer_request_status_t (*request_stream_buffers)(
            const struct camera3_callback_ops *,
            uint32_t num_buffer_reqs,
            const camera3_buffer_request_t *buffer_reqs,
            /*out*/uint32_t *num_returned_buf_reqs,
            /*out*/camera3_stream_buffer_ret_t *returned_buf_reqs);
    void (*return_stream_buffers)(
            const struct camera3_callback_ops *,
            uint32_t num_buffers,
            const camera3_stream_buffer_t* const* buffers);
} camera3_callback_ops_t;
```

#### camera3_capture_result_t : camera3_capture_result

```c
typedef struct camera3_capture_result {
    uint32_t frame_number;
    const camera_metadata_t *result;
    uint32_t num_output_buffers;
    const camera3_stream_buffer_t *output_buffers;
    const camera3_stream_buffer_t *input_buffer;
    uint32_t partial_result;
    uint32_t num_physcam_metadata;
    const char **physcam_ids;
    const camera_metadata_t **physcam_metadata;
} camera3_capture_result_t;
```

#### camera3_capture_request_t : camera3_capture_request

```c
typedef struct camera3_capture_request {
    uint32_t frame_number;
    const camera_metadata_t *settings;
    camera3_stream_buffer_t *input_buffer;
    uint32_t num_output_buffers;
    const camera3_stream_buffer_t *output_buffers;
    uint32_t num_physcam_settings;
    const char **physcam_id;
    const camera_metadata_t **physcam_settings;
} camera3_capture_request_t;
```

#### camera3_request_template_t : camera3_request_template

```dart
typedef enum camera3_request_template {
    CAMERA3_TEMPLATE_PREVIEW = 1,
    CAMERA3_TEMPLATE_STILL_CAPTURE = 2,
    CAMERA3_TEMPLATE_VIDEO_RECORD = 3,
    CAMERA3_TEMPLATE_VIDEO_SNAPSHOT = 4,
    CAMERA3_TEMPLATE_ZERO_SHUTTER_LAG = 5,
    CAMERA3_TEMPLATE_MANUAL = 6,
    CAMERA3_TEMPLATE_COUNT,
    CAMERA3_VENDOR_TEMPLATE_START = 0x40000000
} camera3_request_template_t;
```

#### camera3_stream_buffer_ret_t:camera3_stream_buffer_ret

```c
typedef struct camera3_stream_buffer_ret {
    camera3_stream_t *stream;
    camera3_stream_buffer_req_status_t status;
    uint32_t num_output_buffers;
    camera3_stream_buffer_t *output_buffers;
} camera3_stream_buffer_ret_t;
```

#### camera3_buffer_request_t:camera3_buffer_request

```c
typedef struct camera3_buffer_request {
    camera3_stream_t *stream;
    uint32_t num_buffers_requested;
} camera3_buffer_request_t;
```

#### camera3_stream_buffer_req_status_t:camera3_stream_buffer_req_status

```c
typedef enum camera3_stream_buffer_req_status {
    CAMERA3_PS_BUF_REQ_OK = 0,
    CAMERA3_PS_BUF_REQ_NO_BUFFER_AVAILABLE = 1,
    CAMERA3_PS_BUF_REQ_MAX_BUFFER_EXCEEDED = 2,
    CAMERA3_PS_BUF_REQ_STREAM_DISCONNECTED = 3,
    CAMERA3_PS_BUF_REQ_UNKNOWN_ERROR = 4,
    CAMERA3_PS_BUF_REQ_NUM_STATUS
} camera3_stream_buffer_req_status_t;
```

#### camera3_buffer_request_status_t:camera3_buffer_request_status

```c
typedef enum camera3_buffer_request_status {
    CAMERA3_BUF_REQ_OK = 0,
    CAMERA3_BUF_REQ_FAILED_PARTIAL = 1,
    CAMERA3_BUF_REQ_FAILED_CONFIGURING = 2,
    CAMERA3_BUF_REQ_FAILED_ILLEGAL_ARGUMENTS = 3,
    CAMERA3_BUF_REQ_FAILED_UNKNOWN = 4,
    CAMERA3_BUF_REQ_NUM_STATUS
} camera3_buffer_request_status_t;
```

#### camera3_notify_msg_t : camera3_notify_msg

```c
typedef struct camera3_notify_msg {
    int type;
    union {
        camera3_error_msg_t error;
        camera3_shutter_msg_t shutter;
        uint8_t generic[32];
    } message;
} camera3_notify_msg_t;
```

#### camera3_shutter_msg_t : camera3_shutter_msg

```c
typedef struct camera3_shutter_msg {
    uint32_t frame_number;
    uint64_t timestamp;
} camera3_shutter_msg_t;
```

#### camera3_error_msg_t : camera3_error_msg

```c
typedef struct camera3_error_msg {
    uint32_t frame_number;
    camera3_stream_t *error_stream;
    int error_code;
} camera3_error_msg_t;
```

#### camera3_error_msg_code_t : camera3_error_msg_code

```c
typedef enum camera3_error_msg_code {
    CAMERA3_MSG_ERROR_DEVICE = 1,
    CAMERA3_MSG_ERROR_REQUEST = 2,
    CAMERA3_MSG_ERROR_RESULT = 3,
    CAMERA3_MSG_ERROR_BUFFER = 4,
    CAMERA3_MSG_NUM_ERRORS
} camera3_error_msg_code_t;
```

#### camera3_msg_type_t : camera3_msg_type

```c
typedef enum camera3_msg_type {
    CAMERA3_MSG_ERROR = 1,
    CAMERA3_MSG_SHUTTER = 2,
    CAMERA3_NUM_MESSAGES
} camera3_msg_type_t;
```

#### camera3_jpeg_blob_t : camera3_jpeg_blob

```c
typedef struct camera3_jpeg_blob {
    uint16_t jpeg_blob_id;
    uint32_t jpeg_size;
} camera3_jpeg_blob_t;
```

#### camera3_stream_buffer_set_t : camera3_stream_buffer_set

```c
typedef struct camera3_stream_buffer_set {
    camera3_stream_t *stream;
    uint32_t num_buffers;
    buffer_handle_t **buffers;
} camera3_stream_buffer_set_t;
```

#### camera3_stream_buffer_t : camera3_stream_buffer

```c
typedef struct camera3_stream_buffer {
    camera3_stream_t *stream;
    buffer_handle_t *buffer;
    int status;
    int acquire_fence;
    int release_fence;
} camera3_stream_buffer_t;
```

#### camera3_buffer_status_t : camera3_buffer_status

```c
typedef enum camera3_buffer_status {
    CAMERA3_BUFFER_STATUS_OK = 0,
    CAMERA3_BUFFER_STATUS_ERROR = 1
} camera3_buffer_status_t;
```

#### camera3_stream_configuration_t : camera3_stream_configuration

```c
typedef struct camera3_stream_configuration {
    uint32_t num_streams;
    camera3_stream_t **streams;
    uint32_t operation_mode;
    const camera_metadata_t *session_parameters;
} camera3_stream_configuration_t;
```

#### camera3_stream_t : camera3_stream

```c
typedef struct camera3_stream {
    int stream_type;
    uint32_t width;
    uint32_t height;
    int format;
    uint32_t usage;
    uint32_t max_buffers;
    void *priv;
    android_dataspace_t data_space;
    int rotation;
    const char* physical_camera_id;
    void *reserved[6];
} camera3_stream_t;
```

#### camera3_stream_configuration_mode_t : camera3_stream_configuration_mode

```c
typedef enum camera3_stream_configuration_mode {
    CAMERA3_STREAM_CONFIGURATION_NORMAL_MODE = 0,
    CAMERA3_STREAM_CONFIGURATION_CONSTRAINED_HIGH_SPEED_MODE = 1,
    CAMERA3_VENDOR_STREAM_CONFIGURATION_MODE_START = 0x8000
} camera3_stream_configuration_mode_t;
```

#### camera3_stream_rotation_t : camera3_stream_rotation

```c
typedef enum camera3_stream_rotation {
    CAMERA3_STREAM_ROTATION_0 = 0,
    CAMERA3_STREAM_ROTATION_90 = 1,
    CAMERA3_STREAM_ROTATION_180 = 2,
    CAMERA3_STREAM_ROTATION_270 = 3
} camera3_stream_rotation_t;
```

#### camera3_stream_type_t : camera3_stream_type

```c
typedef enum camera3_stream_type {
    CAMERA3_STREAM_OUTPUT = 0,
    CAMERA3_STREAM_INPUT = 1,
    CAMERA3_STREAM_BIDIRECTIONAL = 2,
    CAMERA3_NUM_STREAM_TYPES
} camera3_stream_type_t;
```


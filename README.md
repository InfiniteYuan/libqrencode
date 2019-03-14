
# QR codes 

QR codes are a type of high-density matrix barcodes.

Branch
=======
- master：The library is very small and fast QRcode
- qrencode-1.0.0: Libqrencode is a fast and compact library for encoding data in a QR Code
- qrencode-3.4.4: Libqrencode is a fast and compact library for encoding data in a QR Code
- qrencode-4.0.2: Libqrencode is a fast and compact library for encoding data in a QR Code
- quirc : quirc is a library for extracting and decoding them from images.


# quirc

quirc is a library for extracting and decoding them from images.

SPECIFICATION
=============
It has several features which make it a good choice for this purpose:

  * It is fast enough to be used with realtime video: extracting and
    decoding from VGA frame takes about 50 ms on a modern x86 core.

  * It has a robust and tolerant recognition algorithm. It can
    correctly recognise and decode QR codes which are rotated and/or
    oblique to the camera. It can also distinguish and decode multiple
    codes within the same image.

  * It is easy to use, with a simple API described in a single
    commented header file (see below for an overview).

  * It is small and easily embeddable, with no dependencies other than
    standard C functions.

  * It has a very small memory footprint: one byte per image pixel,
    plus a few kB per decoder object.

  * It uses no global mutable state, and is safe to use in a
    multithreaded application.

  * BSD-licensed, with almost no restrictions regarding use and/or
    modification.

quirc USAGE
=======
```
void qr_recoginze_task(void *parameter)
{
    camera_config_t *camera_config = (camera_config_t *)parameter;
    // Use VGA Size currently, but quirc can support other frame size.(eg: FRAMESIZE_SVGA,FRAMESIZE_VGA，
    // FRAMESIZE_CIF,FRAMESIZE_QVGA,FRAMESIZE_HQVGA,FRAMESIZE_QCIF,FRAMESIZE_QQVGA2,FRAMESIZE_QQVGA,etc)
    if (camera_config->frame_size > FRAMESIZE_VGA) {
        ESP_LOGE(TAG, "Camera Frame Size err %d", (camera_config->frame_size));
        vTaskDelete(NULL);
    }

    // Save image width and height, avoid allocate memory repeatly.
    uint16_t old_width = 0;
    uint16_t old_height = 0;

    // Construct a new QR-code recognizer.
    ESP_LOGI(TAG, "Construct a new QR-code recognizer(quirc).");
    struct quirc *qr_recognizer = quirc_new();
    if (!qr_recognizer) {
        ESP_LOGE(TAG, "Can't create quirc object");
    }

    while (1) {
        // Capture a frame
        camera_fb_t *fb = esp_camera_fb_get();
        if (!fb) {
            ESP_LOGE(TAG, "Camera capture failed");
            continue;
        }

        if (old_width != fb->width || old_height != fb->height) {
            ESP_LOGD(TAG, "Recognizer size change w h len: %d, %d, %d", fb->width, fb->height, fb->len);
            ESP_LOGI(TAG, "Resize the QR-code recognizer.");
            // Resize the QR-code recognizer.
            if (quirc_resize(qr_recognizer, fb->width, fb->height) < 0) {
                ESP_LOGE(TAG, "Resize the QR-code recognizer err.");
                continue;
            } else {
                old_width = fb->width;
                old_height = fb->height;
            }
        }

        /** These functions are used to process images for QR-code recognition.
         * quirc_begin() must first be called to obtain access to a buffer into
         * which the input image should be placed. Optionally, the current
         * width and height may be returned.
         *
         * After filling the buffer, quirc_end() should be called to process
         * the image for QR-code recognition. The locations and content of each
         * code may be obtained using accessor functions described below.
         */
        uint8_t *image = quirc_begin(qr_recognizer, NULL, NULL);
        memcpy(image, fb->buf, fb->len);
        quirc_end(qr_recognizer);

        // Return the number of QR-codes identified in the last processed image.
        int id_count = quirc_count(qr_recognizer);
        if (id_count == 0) {
            ESP_LOGE(TAG, "Error: not a valid qrcode");
            esp_camera_fb_return(fb);
            continue;
        }

        // Print information of QR-code
        dump_info(qr_recognizer, id_count);
        esp_camera_fb_return(fb);
    }
    // Destroy QR-Code recognizer (quirc)
    quirc_destroy(qr_recognizer);
    ESP_LOGI(TAG, "Deconstruct QR-Code recognizer(quirc)");
    vTaskDelete(NULL);
}
```

# qrencode - a fast and compact QR Code encoding library

GENERAL INFORMATION
===================
Libqrencode is a fast and compact library for encoding data in a QR Code,
a 2D symbology that can be scanned by handy terminals such as a smartphone.
The capacity of QR Code is up to 7000 digits or 4000 characters and has high
robustness.

Libqrencode accepts a string or a list of data chunks then encodes in a QR Code
symbol as a bitmap array. While other QR Code applications generate an image
file, using libqrencode allows applications to render QR Code symbols from raw
bitmap data directly. This library also contains a command-line utility outputs
QR Code images in various formats.


SPECIFICATION
=============
Libqrencode supports QR Code model 2, described in JIS (Japanese Industrial
Standards) X0510:2004 or ISO/IEC 18004. Most of features in the specification
are implemented such as:

- Numeric, alphabet, Japanese kanji (Shift-JIS) or any 8 bit code can be
  embedded
- Optimized encoding of a string
- Structured-append of symbols
- Micro QR Code (experimental)

Currently the following features are not supported:

- ECI and FNC1 mode
- QR Code model 1 (deprecated)

qrencode USAGE
=======
```
/*Fill an area with a color on the display*/
void fill_area(int32_t x1, int32_t y1, int32_t x2, int32_t y2, uint16_t color)
{
    ………………
}

static void show_qrcode()
{
    int i,j,x,y,p;
    EncodeData("https://github.com/InfiniteYuan1?tab=repositories");

    if(m_nSymbleSize*2>LCD_WIDTH)
    {
        printf("qzhu size is too large\n");
        return;
    }

    for(i=0;i<10;i++)
    {
        if((m_nSymbleSize*i*2)>LCD_WIDTH)	break;
    }
    p = (i-1)*2;
    x = (LCD_WIDTH-m_nSymbleSize*p)/2;
    y = LCD_HEIGHT-LCD_WIDTH;

    for(i=0;i<m_nSymbleSize;i++)
    {
        for(j=0;j<m_nSymbleSize;j++)
        {
            if(m_byModuleData[i][j]==1)
            {					
                fill_area(x+p*i,y+p*j,x+p*(i+1),y+p*(j+1), 0x0);// 填充
            }
        }
    }
}
```

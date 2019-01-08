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

USAGE
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

Branch
=======
- master：The library is very small and fast QRcode
- qrencode-1.0.0
- qrencode-3.4.4
- qrencode-4.0.2
- quirc : QR Code decoding library implemented with C.

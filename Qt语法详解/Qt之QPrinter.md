---
title: Qt之QPrinter
categories: Qt语法详解
abbrlink: bb3a90ee
date: 2019-02-18 11:15:19
---
&emsp;&emsp;The `QPrinter` class is a paint device that paints on a printer.

Header     | Inherits
-----------|---------
`QPrinter` | `QPaintDevice`

**Note**: All functions in this class are reentrant.

### Public Functions

Return               | Function
---------------------|---------
                     | `QPrinter(PrinterMode mode = ScreenResolution)`
                     | `QPrinter(const QPrinterInfo & printer, PrinterMode mode = ScreenResolution)`
                     | `~QPrinter()`
`bool`               | `abort()`
`bool`               | `collateCopies() const`
`ColorMode`          | `colorMode() const`
`int`                | `copyCount() const`
`QString`            | `creator() const`
`QString`            | `docName() const`
`bool`               | `doubleSidedPrinting() const`
`DuplexMode`         | `duplex() const`
`bool`               | `fontEmbeddingEnabled() const`
`int`                | `fromPage() const`
`bool`               | `fullPage() const`
`void`               | `getPageMargins(qreal * left, qreal * top, qreal * right, qreal * bottom, Unit unit) const`
`bool`               | `isValid() const`
`bool`               | `newPage()`
`Orientation`        | `orientation() const`
`QString`            | `outputFileName() const`
`OutputFormat`       | `outputFormat() const`
`PageOrder`          | `pageOrder() const`
`QRect`              | `pageRect() const`
`QRectF`             | `pageRect(Unit unit) const`
`QRect`              | `paperRect() const`
`QRectF`             | `paperRect(Unit unit) const`
`PaperSize`          | `paperSize() const`
`QSizeF`             | `paperSize(Unit unit) const`
`PaperSource`        | `paperSource() const`
`QPrintEngine *`     | `printEngine() const`
`QString`            | `printProgram() const`
`PrintRange`         | `printRange() const`
`QString`            | `printerName() const`
`QString`            | `printerSelectionOption() const`
`PrinterState`       | `printerState() const`
`int`                | `resolution() const`
`void`               | `setCollateCopies(bool collate)`
`void`               | `setColorMode(ColorMode newColorMode)`
`void`               | `setCopyCount(int count)`
`void`               | `setCreator(const QString & creator)`
`void`               | `setDocName(const QString & name)`
`void`               | `setDoubleSidedPrinting(bool doubleSided)`
`void`               | `setDuplex(DuplexMode duplex)`
`void`               | `setFontEmbeddingEnabled(bool enable)`
`void`               | `setFromTo(int from, int to)`
`void`               | `setFullPage(bool fp)`
`void`               | `setOrientation(Orientation orientation)`
`void`               | `setOutputFileName(const QString & fileName)`
`void`               | `setOutputFormat(OutputFormat format)`
`void`               | `setPageMargins(qreal left, qreal top, qreal right, qreal bottom, Unit unit)`
`void`               | `setPageOrder(PageOrder pageOrder)`
`void`               | `setPaperSize(PaperSize newPaperSize)`
`void`               | `setPaperSize(const QSizeF & paperSize, Unit unit)`
`void`               | `setPaperSource(PaperSource source)`
`void`               | `setPrintProgram(const QString & printProg)`
`void`               | `setPrintRange(PrintRange range)`
`void`               | `setPrinterName(const QString & name)`
`void`               | `setPrinterSelectionOption(const QString & option)`
`void`               | `setResolution(int dpi)`
`void`               | `setWinPageSize(int pageSize)`
`QList<PaperSource>` | `supportedPaperSources() const`
`QList<int>`         | `supportedResolutions() const`
`bool`               | `supportsMultipleCopies() const`
`int`                | `toPage() const`
`int`                | `winPageSize() const`

### Reimplemented Public Functions

- `virtual QPaintEngine * paintEngine() const;`

### Protected Functions

- `void setEngines(QPrintEngine * printEngine, QPaintEngine * paintEngine);`

### Detailed Description

&emsp;&emsp;The `QPrinter` class is a paint device that paints on a printer.
&emsp;&emsp;This device represents a series of pages of printed output, and is used in almost exactly the same way as other paint devices such as `QWidget` and `QPixmap`. A set of additional functions are provided to manage `device-specific` features, such as orientation and resolution, and to step through the pages in a document as it is generated.
&emsp;&emsp;When printing directly to a printer on `Windows` or `Mac OS X`, `QPrinter` uses the `built-in` printer drivers. On `X11`, `QPrinter` uses the `Common Unix Printing System` (`CUPS`) or the standard `Unix` lpr utility to send `PostScript` or `PDF` output to the printer. As an alternative, the `printProgram()` function can be used to specify the command or utility to use instead of the system default.
&emsp;&emsp;Note that setting parameters like paper size and resolution on an invalid printer is undefined. You can use `QPrinter::isValid()` to verify this before changing any parameters.
&emsp;&emsp;`QPrinter` supports a number of parameters, most of which can be changed by the end user through a print dialog. In general, `QPrinter` passes these functions onto the underlying `QPrintEngine`.
&emsp;&emsp;The most important parameters are:

- `setOrientation()` tells `QPrinter` which page orientation to use.
- `setPaperSize()` tells `QPrinter` what paper size to expect from the printer.
- `setResolution()` tells `QPrinter` what resolution you wish the printer to provide, in dots per inch (`DPI`).
- `setFullPage()` tells `QPrinter` whether you want to deal with the full page or just with the part the printer can draw on.
- `setCopyCount()` tells `QPrinter` how many copies of the document it should print.

&emsp;&emsp;Many of these functions can only be called before the actual printing begins (i.e., before `QPainter::begin()` is called). This usually makes sense because, for example, it's not possible to change the number of copies when you are halfway through printing. There are also some settings that the user sets (through the printer dialog) and that applications are expected to obey.
&emsp;&emsp;When `QPainter::begin()` is called, the `QPrinter` it operates on is prepared for a new page, enabling the `QPainter` to be used immediately to paint the first page in a document. Once the first page has been painted, `newPage()` can be called to request a new blank page to paint on, or `QPainter::end()` can be called to finish printing. The second page and all following pages are prepared using a call to `newPage()` before they are painted.
&emsp;&emsp;The first page in a document does not need to be preceded by a call to `newPage()`. You only need to calling `newPage()` after `QPainter::begin()` if you need to insert a blank page at the beginning of a printed document. Similarly, calling `newPage()` after the last page in a document is painted will result in a trailing blank page appended to the end of the printed document.
&emsp;&emsp;If you want to abort the print job, `abort()` will try its best to stop printing. It may cancel the entire job or just part of it.
&emsp;&emsp;Since `QPrinter` can print to any `QPrintEngine` subclass, it is possible to extend printing support to cover new types of printing subsystem by subclassing `QPrintEngine` and reimplementing its interface.

### Member Type Documentation

- enum `QPrinter::ColorMode`: This enum type is used to indicate whether `QPrinter` should print in color or not.

Constant              | Value | Description
----------------------|-------|------------
`QPrinter::Color`     | `1`   | print in color if available, otherwise in grayscale.
`QPrinter::GrayScale` | `0`   | print in grayscale, even on color printers.

- enum `QPrinter::DuplexMode`: This enum is used to indicate whether printing will occur on one or both sides of each sheet of paper (simplex or duplex printing).

Constant                    | Value | Description
----------------------------|-------|-------------
`QPrinter::DuplexNone`      | `0`   | Single sided (simplex) printing only.
`QPrinter::DuplexAuto`      | `1`   | The printer's default setting is used to determine whether duplex printing is used.
`QPrinter::DuplexLongSide`  | `2`   | Both sides of each sheet of paper are used for printing. The paper is turned over its longest edge before the second side is printed
`QPrinter::DuplexShortSide` | `3`   | Both sides of each sheet of paper are used for printing. The paper is turned over its shortest edge before the second side is printed

- enum `QPrinter::Orientation`: This enum type (not to be confused with `Orientation`) is used to specify each page's orientation.

Constant              | Value | Description
----------------------|-------|------------
`QPrinter::Portrait`  | `0`   | the page's height is greater than its width.
`QPrinter::Landscape` | `1`   | the page's width is greater than its height.

This type interacts with `QPrinter::PaperSize` and `QPrinter::setFullPage()` to determine the final size of the page available to the application.

- enum `QPrinter::OutputFormat`: The `OutputFormat` enum is used to describe the format `QPrinter` should use for printing.

Constant                     | Value | Description
-----------------------------|-------|-------------
`QPrinter::NativeFormat`     | `0`   | `QPrinter` will print output using a method defined by the platform it is running on. This mode is the default when printing directly to a printer.
`QPrinter::PdfFormat`        | `1`   | `QPrinter` will generate its output as a searchable `PDF` file. This mode is the default when printing to a file.
`QPrinter::PostScriptFormat` | `2`   | `QPrinter` will generate its output as in the `PostScript` format.

- enum `QPrinter::PageOrder`: This enum type is used by `QPrinter` to tell the application program how to print.

Constant                   | Value | Description
---------------------------|-------|------------
`QPrinter::FirstPageFirst` | `0`   | the `lowest-numbered` page should be printed first.
`QPrinter::LastPageFirst`  | `1`   | the `highest-numbered` page should be printed first.

- enum `QPrinter::PaperSize`: This enum type specifies what paper size `QPrinter` should use. `QPrinter` does not check that the paper size is available; it just uses this information, together with `QPrinter::Orientation` and `QPrinter::setFullPage()`, to determine the printable area.

&emsp;&emsp;The defined sizes (with `setFullPage(true)`) are:

Constant              | Value | Description
----------------------|-------|------------
`QPrinter::A0`        | `5`   | `841 x 1189` mm
`QPrinter::A1`        | `6`   | `594 x 841` mm
`QPrinter::A2`        | `7`   | `420 x 594` mm
`QPrinter::A3`        | `8`   | `297 x 420` mm
`QPrinter::A4`        | `0`   | `210 x 297` mm, `8.26 x 11.69` inches
`QPrinter::A5`        | `9`   | `148 x 210` mm
`QPrinter::A6`        | `10`  | `105 x 148` mm
`QPrinter::A7`        | `11`  | `74 x 105` mm
`QPrinter::A8`        | `12`  | `52 x 74` mm
`QPrinter::A9`        | `13`  | `37 x 52` mm
`QPrinter::B0`        | `14`  | `1000 x 1414` mm
`QPrinter::B1`        | `15`  | `707 x 1000` mm
`QPrinter::B2`        | `17`  | `500 x 707` mm
`QPrinter::B3`        | `18`  | `353 x 500` mm
`QPrinter::B4`        | `19`  | `250 x 353` mm
`QPrinter::B5`        | `1`   | `176 x 250` mm, `6.93 x 9.84` inches
`QPrinter::B6`        | `20`  | `125 x 176` mm
`QPrinter::B7`        | `21`  | `88 x 125` mm
`QPrinter::B8`        | `22`  | `62 x 88` mm
`QPrinter::B9`        | `23`  | `33 x 62` mm
`QPrinter::B10`       | `16`  | `31 x 44` mm
`QPrinter::C5E`       | `24`  | `163 x 229` mm
`QPrinter::Comm10E`   | `25`  | `105 x 241` mm, U.S. Common `10` Envelope
`QPrinter::DLE`       | `26`  | `110 x 220` mm
`QPrinter::Executive` | `4`   | `7.5 x 10` inches, `190.5 x 254` mm
`QPrinter::Folio`     | `27`  | `210 x 330` mm
`QPrinter::Ledger`    | `28`  | `431.8 x 279.4` mm
`QPrinter::Legal`     | `3`   | `8.5 x 14` inches, `215.9 x 355.6` mm
`QPrinter::Letter`    | `2`   | `8.5 x 11` inches, `215.9 x 279.4` mm
`QPrinter::Tabloid`   | `29`  | `279.4 x 431.8` mm
`QPrinter::Custom`    | `30`  | Unknown, or a user defined size.

With `setFullPage(false)` (the default), the metrics will be a bit smaller; how much depends on the printer in use.

- enum `QPrinter::PaperSource`: This enum type specifies what paper source `QPrinter` is to use. `QPrinter` does not check that the paper source is available; it just uses this information to try and set the paper source. Whether it will set the paper source depends on whether the printer has that particular source. **Warning**: This is currently only implemented for `Windows`.

Constant                   | Value
---------------------------|------
`QPrinter::Auto`           | `6`
`QPrinter::Cassette`       | `11`
`QPrinter::Envelope`       | `4`
`QPrinter::EnvelopeManual` | `5`
`QPrinter::FormSource`     | `12`
`QPrinter::LargeCapacity`  | `10`
`QPrinter::LargeFormat`    | `9`
`QPrinter::Lower`          | `1`
`QPrinter::MaxPageSource`  | `13`
`QPrinter::Middle`         | `2`
`QPrinter::Manual`         | `3`
`QPrinter::OnlyOne`        | `0`
`QPrinter::Tractor`        | `7`
`QPrinter::SmallFormat`    | `8`

- enum `QPrinter::PrintRange`: Used to specify the print range selection option.

Constant                | Value | Description
------------------------|-------|------------
`QPrinter::AllPages`    | `0`   | All pages should be printed.
`QPrinter::Selection`   | `1`   | Only the selection should be printed.
`QPrinter::PageRange`   | `2`   | The specified page range should be printed.
`QPrinter::CurrentPage` | `3`   | Only the current page should be printed.

- enum `QPrinter::PrinterMode`: This enum describes the mode the printer should work in. It basically presets a certain resolution and working mode.

Constant                      | Value | Description
------------------------------|-------|------------
`QPrinter::ScreenResolution`  | `0`   | Sets the resolution of the print device to the screen resolution. This has the big advantage that the results obtained when painting on the printer will match more or less exactly the visible output on the screen. It is the easiest to use, as font metrics on the screen and on the printer are the same. This is the default value. `ScreenResolution` will produce a lower quality output than `HighResolution` and should only be used for drafts.
`QPrinter::PrinterResolution` | `1`   | This value is deprecated. Is is equivalent to `ScreenResolution` on `Unix` and `HighResolution` on `Windows` and `Mac`. Due do the difference between `ScreenResolution` and `HighResolution`, use of this value may lead to `non-portable` printer code.
`QPrinter::HighResolution`    | `2`   | On `Windows`, sets the printer resolution to that defined for the printer in use. For `PostScript` printing, sets the resolution of the `PostScript` driver to `1200` dpi.

**Note**: When rendering text on a `QPrinter` device, it is important to realize that the size of text, when specified in points, is independent of the resolution specified for the device itself. Therefore, it may be useful to specify the font size in pixels when combining text with graphics to ensure that their relative sizes are what you expect.

- enum `QPrinter::PrinterState`:

Constant            | Value
--------------------|------
`QPrinter::Idle`    | `0`
`QPrinter::Active`  | `1`
`QPrinter::Aborted` | `2`
`QPrinter::Error`   | `3`

- enum `QPrinter::Unit`: This enum type is used to specify the measurement unit for page and paper sizes.

Constant                | Value
------------------------|------
`QPrinter::Millimeter`  | `0`
`QPrinter::Point`       | `1`
`QPrinter::Inch`        | `2`
`QPrinter::Pica`        | `3`
`QPrinter::Didot`       | `4`
`QPrinter::Cicero`      | `5`
`QPrinter::DevicePixel` | `6`

Note the difference between `Point` and `DevicePixel`. The `Point` unit is defined to be `1/72th` of an inch, while the `DevicePixel` unit is resolution dependant and is based on the actual pixels, or dots, on the printer.

### Member Function Documentation

- `QPrinter::QPrinter(PrinterMode mode = ScreenResolution)`: Creates a new printer object with the given `mode`.
- `QPrinter::QPrinter(const QPrinterInfo & printer, PrinterMode mode = ScreenResolution)`: Creates a new printer object with the given `printer` and `mode`.
- `QPrinter::~QPrinter()`: Destroys the printer object and frees any allocated resources. If the printer is destroyed while a print job is in progress this may or may not affect the print job.
- `bool QPrinter::abort()`: Aborts the current print run. Returns `true` if the print run was successfully aborted and `printerState()` will return `QPrinter::Aborted`; otherwise returns `false`. It is not always possible to abort a print job. For example, all the data has gone to the printer but the printer cannot or will not cancel the job when asked to.
- `bool QPrinter::collateCopies() const`: Returns `true` if collation is turned on when multiple copies is selected. Returns `false` if it is turned off when multiple copies is selected. When collating is turned off the printing of each individual page will be repeated the `numCopies()` amount before the next page is started. With collating turned on all pages are printed before the next copy of those pages is started.
- `ColorMode QPrinter::colorMode() const`: Returns the current color mode.
- int `QPrinter::copyCount() const`: Returns the number of copies that will be printed. The default value is `1`.
- `QString QPrinter::creator() const`: Returns the name of the application that created the document.
- `QString QPrinter::docName() const`: Returns the document name.
- `bool QPrinter::doubleSidedPrinting() const`: Returns `true` if double side printing is enabled. Currently this option is only supported on `X11`.
- `DuplexMode QPrinter::duplex() const`: Returns the current duplex mode. Currently this option is only supported on `X11`.
- `bool QPrinter::fontEmbeddingEnabled() const`: Returns `true` if font embedding is enabled. Currently this option is only supported on `X11`.
- `int QPrinter::fromPage() const`: Returns the number of the first page in a range of pages to be printed (the `from page` setting). Pages in a document are numbered according to the convention that the first page is page `1`. By default, this function returns a special value of `0`, meaning that the `from page` setting is unset. **Note**: If `fromPage()` and `toPage()` both return `0`, this indicates that the whole document will be printed.
- `bool QPrinter::fullPage() const`: Returns `true` if the origin of the printer's coordinate system is at the corner of the page and `false` if it is at the edge of the printable area.
- `void QPrinter::getPageMargins(qreal * left, qreal * top, qreal * right, qreal * bottom, Unit unit) const`: Returns the page margins for this printer in `left`, `top`, `right`, `bottom`. The unit of the returned margins are specified with the `unit` parameter.
- `bool QPrinter::isValid() const`: Returns `true` if the printer currently selected is a valid printer in the system, or a pure `PDF/PostScript` printer; otherwise returns `false`. To detect other failures check the output of `QPainter::begin()` or `QPrinter::newPage()`.

``` cpp
QPrinter printer;
printer.setOutputFormat ( QPrinter::PdfFormat );
printer.setOutputFileName ( "/foobar/nonwritable.pdf" );
QPainter painter;
​
if ( ! painter.begin ( &printer ) ) { /* failed to open file */
    qWarning ( "failed to open file, is it writable?" );
    return 1;
}
​
painter.drawText ( 10, 10, "Test" );
​
if ( ! printer.newPage() ) {
    qWarning ( "failed in flushing page to disk, disk full?" );
    return 1;
}
​
painter.drawText ( 10, 10, "Test 2" );
painter.end();
```

- `bool QPrinter::newPage()`: Tells the printer to eject the current page and to continue printing on a new page. Returns `true` if this was successful; otherwise returns `false`. Calling `newPage()` on an inactive `QPrinter` object will always fail.
- `Orientation QPrinter::orientation() const`: Returns the orientation setting. This is `driver-dependent`, but is usually `QPrinter::Portrait`.
- `QString QPrinter::outputFileName() const`: Returns the name of the output file. By default, this is an empty string (indicating that the printer shouldn't print to file).
- `OutputFormat QPrinter::outputFormat() const`: Returns the output format for this printer.
- `PageOrder QPrinter::pageOrder() const`: Returns the current page order. The default page order is `FirstPageFirst`.
- `QRect QPrinter::pageRect() const`: Returns the page's rectangle; this is usually smaller than the `paperRect()` since the page normally has margins between its borders and the paper. The unit of the returned rectangle is `DevicePixel`.
- `QRectF QPrinter::pageRect(Unit unit) const`: Returns the page's rectangle in `unit`; this is usually smaller than the `paperRect()` since the page normally has margins between its borders and the paper.
- `QPaintEngine * QPrinter::paintEngine() const [virtual]`: Reimplemented from `QPaintDevice::paintEngine()`. Returns the paint engine used by the printer.
- `QRect QPrinter::paperRect() const`: Returns the paper's rectangle; this is usually larger than the `pageRect()`. The unit of the returned rectangle is `DevicePixel`.
- `QRectF QPrinter::paperRect(Unit unit) const`: Returns the paper's rectangle in `unit`; this is usually larger than the `pageRect()`.
- `PaperSize QPrinter::paperSize() const`: Returns the printer paper size. The default value is `driver-dependent`.
- `QSizeF QPrinter::paperSize(Unit unit) const`: Returns the paper size in `unit`.
- `PaperSource QPrinter::paperSource() const`: Returns the printer's paper source. This is `Manual` or a printer tray or paper cassette.
- `QPrintEngine * QPrinter::printEngine() const`: Returns the print engine used by the printer.
- `QString QPrinter::printProgram() const`: Returns the name of the program that sends the print output to the printer. The default is to return an empty string; meaning that `QPrinter` will try to be smart in a `system-dependent` way. On `X11` only, you can set it to something different to use a specific print program. On the other platforms, this returns an empty string.
- `PrintRange QPrinter::printRange() const`: Returns the page range of the `QPrinter`. After the print setup dialog has been opened, this function returns the value selected by the user.
- `QString QPrinter::printerName() const`: Returns the printer name. This value is initially set to the name of the default printer.
- `QString QPrinter::printerSelectionOption() const`: Returns the printer options selection string. This is useful only if the print command has been explicitly set. The default value (an empty string) implies that the printer should be selected in a `system-dependent` manner. Any other value implies that the given value should be used. **Warning**: This function is not available on `Windows`.
- `PrinterState QPrinter::printerState() const`: Returns the current state of the printer. This may not always be accurate (for example if the printer doesn't have the capability of reporting its state to the operating system).
- `int QPrinter::resolution() const`: Returns the current assumed resolution of the printer, as set by `setResolution()` or by the printer driver.
- `void QPrinter::setCollateCopies(bool collate)`: Sets the default value for collation checkbox when the print dialog appears. If `collate` is `true`, it will enable `setCollateCopiesEnabled()`. The default value is `false`. This value will be changed by what the user presses in the print dialog.
- `void QPrinter::setColorMode(ColorMode newColorMode)`: Sets the printer's color mode to `newColorMode`, which can be either `Color` or `GrayScale`.
- `void QPrinter::setCopyCount(int count)`: Sets the number of copies to be printed to `count`. The printer driver reads this setting and prints the specified number of copies.
- `void QPrinter::setCreator(const QString & creator)`: Sets the name of the application that created the document to `creator`. This function is only applicable to the `X11` version of `Qt`. If no `creator` name is specified, the `creator` will be set to `Qt` followed by some version number.
- `void QPrinter::setDocName(const QString & name)`: Sets the document name to `name`. On `X11`, the document name is for example used as the default output filename in `QPrintDialog`. Note that the document name does not affect the file name if the printer is printing to a file. Use the `setOutputFile()` function for this.
- `void QPrinter::setDoubleSidedPrinting(bool doubleSided)`: Enables double sided printing if `doubleSided` is `true`; otherwise disables it. Currently this option is only supported on `X11`.
- `void QPrinter::setDuplex(DuplexMode duplex)`: Enables double sided printing based on the `duplex` mode. Currently this option is only supported on `X11`.
- `void QPrinter::setEngines(QPrintEngine * printEngine, QPaintEngine * paintEngine) [protected]`: This function is used by subclasses of `QPrinter` to specify custom print and paint engines (`printEngine` and `paintEngine`, respectively). `QPrinter` does not take ownership of the engines, so you need to manage these engine instances yourself. Note that changing the engines will reset the printer state and all its properties.
- `void QPrinter::setFontEmbeddingEnabled(bool enable)`: Enabled or disables font embedding depending on `enable`. Currently this option is only supported on `X11`.
- `void QPrinter::setFromTo(int from, int to)`: Sets the range of pages to be printed to cover the pages with numbers specified by `from` and `to`, where `from` corresponds to the first page in the range and `to` corresponds to the last. **Note**: Pages in a document are numbered according to the convention that the first page is page `1`. However, if `from` and `to` are both set to `0`, the whole document will be printed. This function is mostly used to set a default value that the user can override in the print dialog when you call `setup()`.
- `void QPrinter::setFullPage(bool fp)`: If `fp` is `true`, enables support for painting over the entire page; otherwise restricts painting to the printable area reported by the device. By default, full page printing is disabled. In this case, the origin of the `QPrinter's` coordinate system coincides with the `top-left` corner of the printable area. If full page printing is enabled, the origin of the `QPrinter's` coordinate system coincides with the `top-left` corner of the paper itself. In this case, the device metrics will report the exact same dimensions as indicated by `PaperSize`. It may not be possible to print on the entire physical page because of the printer's margins, so the application must account for the margins itself.
- `void QPrinter::setOrientation(Orientation orientation)`: Sets the print orientation to `orientation`. The `orientation` can be either `QPrinter::Portrait` or `QPrinter::Landscape`. The printer driver reads this setting and prints using the specified orientation. On `Windows`, this option can be changed while printing and will take effect from the next call to `newPage()`. On `Mac OS X`, changing the orientation during a print job has no effect.
- `void QPrinter::setOutputFileName(const QString & fileName)`: Sets the name of the output file to `fileName`. Setting a null or empty name (`0` or `""`) disables printing to a file. Setting a `non-empty` name enables printing to a file. This can change the value of `outputFormat()`. If the file name has the suffix `.ps` then `PostScript` is automatically selected as output format. If the file name has the `.pdf` suffix `PDF` is generated. If the file name has a suffix other than `.ps` and `.pdf`, the output format used is the one set with `setOutputFormat()`. `QPrinter` uses `Qt's` `cross-platform` `PostScript` or `PDF` print engines respectively. If you can produce this format natively, for example `Mac OS X` can generate `PDF's` from its print engine, set the output format back to `NativeFormat`.
- `void QPrinter::setOutputFormat(OutputFormat format)`: Sets the output format for this printer to `format`.
- `void QPrinter::setPageMargins(qreal left, qreal top, qreal right, qreal bottom, Unit unit)`: This function sets the `left`, `top`, `right` and `bottom` page margins for this printer. The unit of the margins are specified with the `unit` parameter.
- `void QPrinter::setPageOrder(PageOrder pageOrder)`: Sets the page order to `pageOrder`. The page order can be `QPrinter::FirstPageFirst` or `QPrinter::LastPageFirst`. The application is responsible for reading the page order and printing accordingly. This function is mostly useful for setting a default value that the user can override in the print dialog. This function is only supported under `X11`.
- `void QPrinter::setPaperSize(PaperSize newPaperSize)`: Sets the printer paper size to `newPaperSize` if that size is supported. The result is undefined if `newPaperSize` is not supported. The default paper size is `driver-dependent`. This function is useful mostly for setting a default value that the user can override in the print dialog.
- `void QPrinter::setPaperSize(const QSizeF & paperSize, Unit unit)`: Sets the paper size based on `paperSize` in `unit`.
- `void QPrinter::setPaperSource(PaperSource source)`: Sets the paper source setting to `source`. `Windows` only: This option can be changed while printing and will take effect from the next call to `newPage()`.
- `void QPrinter::setPrintProgram(const QString & printProg)`: Sets the name of the program that should do the print job to `printProg`. On `X11`, this function sets the program to call with the `PostScript` output. On other platforms, it has no effect.
- `void QPrinter::setPrintRange(PrintRange range)`: Sets the print range option in to be `range`.
- `void QPrinter::setPrinterName(const QString & name)`: Sets the printer name to `name`.
- `void QPrinter::setPrinterSelectionOption(const QString & option)`: Sets the printer to use `option` to select the printer. `option` is null by default (which implies that `Qt` should be smart enough to guess correctly), but it can be set to other values to use a specific printer selection option. If the printer selection option is changed while the printer is active, the current print job may or may not be affected. Warning: This function is not available on `Windows`.
- `void QPrinter::setResolution(int dpi)`: Requests that the printer prints at `dpi` or as near to `dpi` as possible. This setting affects the coordinate system as returned by, for example `QPainter::viewport()`. This function must be called before `QPainter::begin()` to have an effect on all platforms.
- `void QPrinter::setWinPageSize(int pageSize)`: Sets the page size to be used by the printer under Windows to `pageSize`. **Warning**: This function is not portable so you may prefer to use `setPaperSize()` instead.
- `QList<PaperSource> QPrinter::supportedPaperSources() const`: Returns the supported paper sizes for this printer. The values will be either a value that matches an entry in the `QPrinter::PaperSource` enum or a driver spesific value. The driver spesific values are greater than the constant `DMBIN_USER` declared in `wingdi.h`. **Warning**: This function is only available in `Windows`.
- `QList<int> QPrinter::supportedResolutions() const`: Returns a list of the resolutions (a list of `dots-per-inch` integers) that the printer says it supports. For `X11` where all printing is directly to postscript, this function will always return a one item list containing only the postscript resolution.
- `bool QPrinter::supportsMultipleCopies() const`: Returns `true` if the printer supports printing multiple copies of the same document in one job; otherwise `false` is returned. On most systems this function will return `true`. However, on `X11` systems that do not support `CUPS`, this function will return `false`. That means the application has to handle the number of copies by printing the same document the required number of times.
- `int QPrinter::toPage() const`: Returns the number of the last page in a range of pages to be printed (the `to page` setting). Pages in a document are numbered according to the convention that the first page is page `1`. By default, this function returns a special value of `0`, meaning that the `to page` setting is unset. **Note**: If `fromPage()` and `toPage()` both return `0`, this indicates that the whole document will be printed. The programmer is responsible for reading this setting and printing accordingly.
- `int QPrinter::winPageSize() const`: Returns the page size used by the printer under `Windows`. **Warning**: This function is not portable so you may prefer to use `paperSize()` instead.
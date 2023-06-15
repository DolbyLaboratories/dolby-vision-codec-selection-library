# dolby-vision-codec-selection-library

The Dolby Vision codec selection library is for Android developers who are adding support for Dolby Vision user-generated content to their applications.  The library helps to ensure that an application can adapt to various Android mobile device capabilities and support Dolby Vision content in playback and transcoding use cases.

The library retrieves track information from an input video file and checks the capabilities of the device to support Dolby Vision. It selects the appropriate track and codec for decoding the video content based on the device's capabilities and the chosen output format (HEVC, AVC, or Dolby Vision Profile 8.4).  It uses standard Android classes including `MediaExtractor`, `MediaFormat`, and `MediaCodec` (see the `import` statements in `CodecBuilderImpl.java` for all Android classes used).

It is important to note that the Dolby Vision codec selection library also considers whether decoding and subsequent color space conversion processes (for example, Dolby Vision Profile 8.4 to SDR) are based on a GPU (graphics processing unit) or a DPU (display processing unit). These processes are straightforward when the DPU is available and capable of performing the necessary HDR to SDR color mapping. In this case, the `MediaFormat` class used to configure the `MediaCodec` decoder needs only to have the `KEY_COLOR_TRANSFER_REQUEST` field set to the appropriate value (`COLOR_TRANSFER_SDR_VIDEO` for SDR). However, when the GPU is used for decoding Dolby Vision and color mapping, it is necessary to configure additional decoder parameters (`vendor.dolby.codec.transfer.value`) with specific color transfer type (for example, `transfer.sdr.normal` for SDR).  The Dolby Vision codec selection library provides a more straightforward interface for selecting and configuring the appropriate Dolby Vision codec, whether it uses the GPU or the DPU.

## Installation

The code available in this repository can be opened as an Android Studio project. Once this is done, build the application as usual. By default, the output will not be an APK (Android Package Kit) file, but instead an AAR (Android Archive) file. This archive file can then be linked to another project, allowing the codec selector to be used as a library.

## Reference documentation

Reference documentation generated using Doxygen is available at `docs/doxygen/html`.  Download this directory and open `docs/doxygen/html/index.html` in a browser to view the reference documentation. 

## Usage

### Codec builder

The `CodecBuilderImpl` class extends the `CodecBuilder` interface and implements codec selection functionality.  

To access `CodecBuilderImpl` and other codec selection library classes, import  the `codecselection` package:
```import com.dolby.vision.codecselection;```  

The constructor for `CodecBuilderImpl` instantiates a builder for an Android context, an output format, and an input file URI:

```java
public CodecBuilderImpl(Context context, String outputFormat, Uri uri) throws IOException;
```

### Builder methods for processor type

The static method `getSupportStatus` of the `CodecBuilderImpl` class returns an enumerated value indicating the type of processor for the platform.  The method and its enumerated return type are declared as follows:

```java
public static enum PlatformSupport
{
   NONE,
   GPU,
   DPU
}

public static PlatformSupport getSupportStatus() {...}
```

There are two static methods of the `CodecBuilderImpl` class to determine whether the decoder name is the name for a specific type of processor (GPU or DPU):

```java
public static boolean isDolbyGPUDecoder(String decoderName) {...}

public static boolean isDolbyDPUDecoder(String decoderName) {...}
```

### Builder method for Dolby support

The `isDolbySupported` method returns a `boolean` value indicating whether Dolby Vision is supported:

```java
public boolean isDolbySupported() {...}
```

### Builder methods for profile and format information

The `getDolbyCodecMapping` method returns a `HashMap` with with Dolby Vision profile numbers mapped to lists of codecs, and can be used to get codecs for a profile:

```java
public HashMap<Integer, ArrayList<String>> getDolbyCodecMapping() {...}
```

The `printDolbyCodecSupport` method returns a `String` with a return-separated list of supported Dolby Vision profile names:

```java
public String printDolbyCodecSupport() {...}
```

The `getDecodedFormat` method returns a `MediaFormat` that is the decoder format indicated by the builder:

```java
public MediaFormat getDecodedFormat() {
        return this.decoderFormat;
}
```

The `getCodecName` method returns the name of the codec for the format of the  track:

```java
public String getCodecName() throws ... {...}
```

### Usage example

The `MainActivity.java` file defines a class `MainActivity` that extends the Android class `AppCompatActivity` and uses a `CodecBuilderImpl` instance when opening a MP4 video file upon creation (`onCreate`).

First, the builder is instantiated and its methods are called to get the codec name and to check for Dolby support (with logging of return values omitted):

```java
CodecBuilderImpl builder = new CodecBuilderImpl(context, "placeholder", inputUri.getData());
builder.printDolbyCodecSupport();

String codecName = builder.getCodecName();

boolean dolby = builder.isDolbySupported();
```

Next, an `ExampleDecoder` is instantiated and used to create a codec, and the builder  creates and configures a `MediaExtractor` for the codec that can be used to play the video:

```java
ExampleDecoder decoder = new ExampleDecoder();

decoder.createCodec(MediaCodec.createByCodecName(codecName));

MediaExtractor extractor = builder.configure(decoder.getCodec(), null, decoder, 1);
```

## License

This project is licensed under the BSD-3 License - see the [LICENSE](https://github.com/DolbyLaboratories/dolby-vision-codec-selection-library/blob/main/LICENSE) file for details.

## Related projects

See the [dolby-vision-editor](https://github.com/DolbyLaboratories/dolby-vision-editor) project.  

See the `VideoDecoder` and `FrameHandler` classes for uses of the `CodecBuilderImpl` class.

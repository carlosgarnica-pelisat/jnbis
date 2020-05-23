# JNBIS #
Java Implementation of NIST Biometric Image Software (NBIS) 
[![GitHub license](https://img.shields.io/badge/license-Apache%20License%202.0-blue.svg?style=flat)](http://www.apache.org/licenses/LICENSE-2.0)

### About JNBIS ###
JNBIS is a library, written in Java, to extract and decode NIST (National Institute of Standards and Technology) compressed files and WSQ (Wavelet Scalar Quantization) images. 
The code has been converted from NBIS (NIST Biometric Image Software) version 1.1 which is written in C.
You can find more about NIST Biometric Image Software [here](http://www.nist.gov/itl/iad/ig/nbis.cfm).

> **NOTE:** This version is a fork of the original JNBIS library by [kareez](https://github.com/kareez/jnbis) and adds basic NIST encoding support. 

### Quick Start ###
##### Build and Install #####
This version/fork is not available through Maven Central. You will need to clone the source code and build it with Maven. You need **JDK** version **1.8 or higher** to build the code.

```bash
$ git clone git@github.com:carlosgarnica-pelisat/jnbis.git
$ cd jnbis
$ mvn install
```

You can then push it to your repository or just use it from your local repository by adding the following dependency to your project pom: 
```xml
<dependency>
  <groupId>jnbis</groupId>
  <artifactId>jnbis</artifactId>
  <version>x.x.x</version>
</dependency>
```

### Examples ###
##### WSQ Decoding ##### 
Convert WSQ image to PNG image and return the result as **File**
```Java
File png = Jnbis.wsq()
                .decode("path/to/wsq/file.wsq")
                .toPng()
                .asFile("/path/to/final/file.png");
```
Convert WSQ image to GIF image and return the result as **File**
 ```Java
File gif = Jnbis.wsq()
                .decode(new File("path/to/wsq/file.wsq"))
                .toGif()
                .asFile("/path/to/final/file.gif");
```
Convert WSQ image (as input stream) to JPEG image and return the result as **File**
```Java
File jpg = Jnbis.wsq()
                .decode(wsqInputStream)
                .toJpg()
                .asFile("/path/to/final/file.jpg");
 ```
 Convert WSQ image to PNG image and return the result as **InputStream** 
```Java
 InputStream pngStream = Jnbis.wsq()
                              .decode("path/to/wsq/file.wsq")
                              .toPng()
                              .asInputStream();
```
Convert WSQ image to GIF image and return the result as **Byte Array**
```Java
byte[] gifBytes = Jnbis.wsq()
                       .decode(new File("path/to/wsq/file.wsq"))
                       .toGif()
                       .asByteArray();
```
 
For more examples check the **SampleWsqTest.java** in the project source. 

##### NIST Decoding ##### 
Decode a NIST file with given file name
```Java
Nist nist = Jnbis.nist().decode("/path/to/nist/file"));
```

Decode a NIST file with given **File** instance
```Java
Nist nist = Jnbis.nist().decode(new File("/path/to/nist/file")));
```

Decode a NIST file with given **InputStream** instance
```Java
Nist nist = Jnbis.nist().decode(nistInputStream));
```

**Nist** instance contains different types of data, depending on file type. 
Here is a sample code that extract all fingerprints and save them in individual files. 
```Java
Nist nist = Jnbis.nist().decode(new File("/path/to/nist/file")));

for (HighResolutionGrayscaleFingerprint fp : nist.getHiResGrayscaleFingerprints()) {
    Jnbis.wsq()
        .decode(fp.getImageData())
        .toPng()
        .asFile("/path/fp-" + fp.getImageDesignationCharacter() + ".png");
}
 ```
For more examples check the **SampleNistTest.java** and **AnsiReferencesTest.java** in the project source. 

##### NIST Encoding #####

Basic encoding of the following types are partially supported (not all fields of Type-14 are supported):
* Type-1
* Type-2
* Type-4
* Type-14

See **NistEncoderTest.java** for examples of how to build up a NIST file containing these records. Here's a quick example
of the overall process:

```Java
        NistBuilder builder = Jnbis.nist()
                .create()
                .transactionInfo(txInfo)
                .add(udt);
        
        for (HighResolutionGrayscaleFingerprint fp: fps) {
            builder.add(fp);
        }

        byte[] encodedNist = builder.build()
                .encode();
```


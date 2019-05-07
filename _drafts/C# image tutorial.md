---
layout: post
title: C# image tutorial
author: Andy Feng
categories: [C#, Image]
---

# Fundamentals #
`Image` class contains the basic function to open and save an image

	```
	public class Image{
		FromFile(string)
		RotateFlip(RotateFlipType)
		Save(string)
		Height {get;set;}
		Width {get;set;}
		Size {get;set;
	}
	```

`Bitmap` class inherits `Image`

	public sealed class Bitmap : Image
	{
		GetPixel(int, int)
		SetPixel(int, int, Color)
		Clone(Rectange, PixelFormat)
		MakeTransparent(Color)
		...
	}

`ImageFormat`

	public sealed class ImageFormat {
	        private static ImageFormat memoryBMP = new ImageFormat(new Guid("{b96b3caa-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat bmp       = new ImageFormat(new Guid("{b96b3cab-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat emf       = new ImageFormat(new Guid("{b96b3cac-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat wmf       = new ImageFormat(new Guid("{b96b3cad-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat jpeg      = new ImageFormat(new Guid("{b96b3cae-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat png       = new ImageFormat(new Guid("{b96b3caf-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat gif       = new ImageFormat(new Guid("{b96b3cb0-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat tiff      = new ImageFormat(new Guid("{b96b3cb1-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat exif      = new ImageFormat(new Guid("{b96b3cb2-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat photoCD   = new ImageFormat(new Guid("{b96b3cb3-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat flashPIX  = new ImageFormat(new Guid("{b96b3cb4-0728-11d3-9d7b-0000f81ef32e}"));
	        private static ImageFormat icon      = new ImageFormat(new Guid("{b96b3cb5-0728-11d3-9d7b-0000f81ef32e}"));
	}

common format:

- BMP
- GIF
- JPEG
- PNG
- TIFF

class `ImageCodecInfo` is an encoder which translates the data in an `Image` or `Bitmap` object into a designated format. 

class `EncoderParameters`. we use it to set up quality and compression on an image with encoder object.

`Graphics` encapsulates a GDI+ drawing surface and allow us to draw on a surface. The surface could be an empty cavas or an existing image. We can draw a line, curve, arc rectangle, text and so on. 
	 
- Load an image

	`using (var bitmap = (Bitmap) Image.FromFile(path)){	}`
	
	or

	`using(var bitmap = new Bitmap(path)){}`

- Save an image

	save to filesystem:

		Save(string filename, ImageCodecInfo encoder, EncoderParameters encoderParams)
		Save(string filename, ImageFormat format)
		Save(string filename)

	save to stream:

		Save(Stream stream, ImageCodecInfo encoder, EncoderParameters encoderParams)
		Save(Stream stream, ImageFormat format)

- draw a line

		using (Graphics gr = Graphics.FromImage(bitmap)){
			gr.DrawString(text, font, brush, PointF, textFormat);
		}

# References #
[https://docs.microsoft.com/en-us/dotnet/framework/winforms/advanced/how-to-draw-an-existing-bitmap-to-the-screen](https://docs.microsoft.com/en-us/dotnet/framework/winforms/advanced/how-to-draw-an-existing-bitmap-to-the-screen)

[https://www.codeproject.com/Articles/9727/Image-Processing-Lab-in-C](https://www.codeproject.com/Articles/9727/Image-Processing-Lab-in-C)

[https://referencesource.microsoft.com/#System.Drawing/commonui/System/Drawing/Advanced/EncoderParameters.cs,5104da4e2d40c2eb](https://referencesource.microsoft.com/#System.Drawing/commonui/System/Drawing/Advanced/EncoderParameters.cs,5104da4e2d40c2eb)
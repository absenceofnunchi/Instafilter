# Instafilter

An iOS app in Swift to demonstrate the use case of Core Image such as CIFilter, CIContext, and CIImage. 

<img src="https://github.com/igibliss00/Instafilter/blob/master/README_assets/final.jpeg" width="400">


## Features 

### Core Image

This project introduces the Core Image framework.  Core Image is Swift and Objective-C’s native solution for still and video processing, namely for applying filters.  Swift and Objective-C contains a sizeable amount of proprietary code hidden and immutable from the developers, which feels prohibiting and liberating at the same time.  Prohibiting in that we can’t do anything about the frameworks given to us and is forced to use in the way they were created, but liberating in that we don’t have to worry about how things work under the hood and we simply have to interact with the application programming interface (API).   Core Image is one of those cases.  

You only have to provide the necessary data types to Core Image and the framework will work its magic with OpenGL, OpenGL ES, or Metal, leveraging the GPU and the multithreading with Grand Central Dispatch (GCD) all on its own.  You don’t have to know how OpenGL works, how to fine-grain control GPU, nor do you have to worry about the GCD.  

### CIImage

A CIImage object is not an image, although it has image data associated with it.  According to Apple’s [documentation](https://developer.apple.com/documentation/coreimage/ciimage), it’s more like an image “recipe”.  In other words, CIImage is not simply to display an image nor does it work alone, but either works with or is a product of other Core Image classes like CIFilter, CIContext, CIVector, and CIColor .

```
guard currentImage != nil else { return }
let beginImage = CIImage(image: currentImage)
currentFilter.setValue(beginImage, forKey: kCIInputImageKey)
```

### Filters

 A filter can be used individually or chained together.  Being chained together, which is called a filter graph, means an output of one filter is becoming the input of another.  One can create custom filters or use the presets created by Apple.  There are also filters that analyze the given image to provide an optimal solution, such as the red-eye filter. 

Following is a category of filters:

<img src="https://github.com/igibliss00/Instafilter/blob/master/README_assets/filters.png" width="600">

* [source](https://developer.apple.com/library/archive/documentation/GraphicsImaging/Conceptual/CoreImaging/ci_concepts/ci_concepts.html#//apple_ref/doc/uid/TP30001185-CH2-TPXREF101)


### CIFilter

An image processor that produces an image by manipulating one or more input images or by generating new image data. he CIFilter class produces a CIImage object as output. Typically, a filter takes one or more images as input. Some filters, however, generate an image based on other types of input parameters. The parameters of a CIFilter object are set and retrieved through the use of key-value pairs.
You use the CIFilter object in conjunction with other Core Image classes, such as CIImage, CIContext, and CIColor, to take advantage of the built-in Core Image filters when processing images, creating filter generators, or writing custom filters. ([Source](https://developer.apple.com/documentation/coreimage/cifilter))

```
guard let actionTitle = action.title else { return }
currentFilter = CIFilter(name: actionTitle)
```

In this project, we get the action title of UIAlertAction that is passed as a parameter, depending on what the user chooses, from the UIAlertcontroller, and use that title as the argument for CIFilter to create an instance.

### CIContext

An evaluation context for rendering image processing results and performing image analysis. The CIContext class provides an evaluation context for Core Image processing with Quartz 2D, Metal, or OpenGL. You use CIContext objects in conjunction with other Core Image classes, such as CIFilter, CIImage, and CIColor, to process images using Core Image filters. You also use a Core Image context with the CIDetector class to analyze images—for example, to detect faces or barcodes. ([Source](https://developer.apple.com/documentation/coreimage/cicontext))

```
if let cgimg = context.createCGImage(image, from: image.extent) {
    let processedImage = UIImage(cgImage: cgimg)
    imageView.image = processedImage
}
```

### Saving to Photos Album

```
@IBAction func save(_ sender: Any) {
    guard let image = imageView.image else { return }
    UIImageWriteToSavedPhotosAlbum(image, self, #selector(image(_:didFinishSavingWithError:contextInfo:)), nil)
}

@objc func image(_ image: UIImage, didFinishSavingWithError error: Error?, contextInfo: UnsafeRawPointer) {
    if let error = error {
        let ac = UIAlertController(title: "Save error", message: error.localizedDescription, preferredStyle: .alert)
        ac.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
        present(ac, animated: true)
    } else {
        let ac = UIAlertController(title: "Saved!", message: "Your altered image has been saved to your photos.", preferredStyle: .alert)
        ac.addAction(UIAlertAction(title: "OK", style: .default))
        present(ac, animated: true)
    }
}
```

<img src="https://github.com/igibliss00/Instafilter/blob/master/README_assets/saved.jpeg" width="400">


### The Execution Flow

This project follows the following order for setting a filter on an image:

1. Instantiate CiContext and CIFilter into a variable named “context” and “currentFilter”, respectively.
2. Set an UIImage that’s been converted to a CIImage as a value to “currentFilter” and “kCIInputImageKey” as the key.
3. Making sure that the current filter is compatible with the intensity value, create a CGIImage using “context”.
4. Convert the CGImage to UIImage and assign it to the UIImageView.
5. Change the filter of your choice by assigning the instance of CIFilter to “currentFilter” and repeat from 2 to 4. Note: the image has to be set to “currentFilter” again when the filter is changed.
6. Save to the photos album.

### Changing the Filter

```
@IBAction func changeFilter(_ sender: Any) {
    let ac = UIAlertController(title: "Choose filter", message: nil, preferredStyle: .actionSheet)
    ac.addAction(UIAlertAction(title: "CIBumpDistortion", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CIGaussianBlur", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CIPixellate", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CISepiaTone", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CITwirlDistortion", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CIUnsharpMask", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "CIVignette", style: .default, handler: setFilter))
    ac.addAction(UIAlertAction(title: "Cancel", style: .cancel))
    
    present(ac, animated: true)
}
```

<img src="https://github.com/igibliss00/Instafilter/blob/master/README_assets/changeFilter.jpeg" width="400">

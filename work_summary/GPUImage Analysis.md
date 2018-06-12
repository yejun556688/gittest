> ###### 环境

- GPUImage上下文对象

| 类名              | 说明            | 解析                                       |
| --------------- | ------------- | ---------------------------------------- |
| GPUImageContext | GPUImage上下文对象 | [参见 GPUImage源码阅读（一）](https://www.jianshu.com/p/7cb6a08efb11) |

- GL程序

| 类名        | 说明     | 解析                                       |
| --------- | ------ | ---------------------------------------- |
| GLProgram | 管理GL程序 | [参见 GPUImage源码阅读（一）](https://www.jianshu.com/p/7cb6a08efb11) |

- 帧缓存

| 类名                       | 说明      | 解析                                       |
| ------------------------ | ------- | ---------------------------------------- |
| GPUImageFramebufferCache | 管理帧缓存对象 | [参见 GPUImage源码阅读（二）](https://www.jianshu.com/p/9f650f11f98e) |
| GPUImageFramebuffer      | 帧缓存对象   | [参见 GPUImage源码阅读（二）](https://www.jianshu.com/p/9f650f11f98e) |

- 基类

| 类名             | 说明           | 解析                                       |
| -------------- | ------------ | ---------------------------------------- |
| GPUImageOutput | 能够输出帧缓存对象的基类 | [参见 GPUImage源码阅读（三）](https://www.jianshu.com/p/bb03366128c4) |

- 协议

| 类名            | 说明              | 解析                                       |
| ------------- | --------------- | ---------------------------------------- |
| GPUImageInput | 被能接受帧缓存对象输入的类实现 | [参见 GPUImage源码阅读（三）](https://www.jianshu.com/p/bb03366128c4) |

> ###### 输入

| 类名                   | 说明     | 解析                                       |
| -------------------- | ------ | ---------------------------------------- |
| GPUImageUIElement    | UI渲染输入 | [参见GPUImage源码阅读（四）](https://www.jianshu.com/p/9b349c04a2dc) |
| GPUImageMovie        | 视频输入   | [参见GPUImage源码阅读（五）](https://www.jianshu.com/p/8f1b7dca9956) |
| GPUImagePicture      | 图片输入   | [参见GPUImage源码阅读（四）](https://www.jianshu.com/p/9b349c04a2dc) |
| GPUImageRawDataInput | 元数据输入  | [参见GPUImage源码阅读（六）](https://www.jianshu.com/p/8b4263d13cf0) |
| GPUImageTextureInput | 纹理输入   | [参见GPUImage源码阅读（六）](https://www.jianshu.com/p/8b4263d13cf0) |
| GPUImageVideoCamera  | 相机视频输入 | [参见GPUImage源码阅读（五）](https://www.jianshu.com/p/8f1b7dca9956) |
| GPUImageStillCamera  | 相机照片输入 | [参见GPUImage源码阅读（五）](https://www.jianshu.com/p/8f1b7dca9956) |

> ###### 滤镜

- 单目标输入滤镜

| 类名             | 说明      | 解析                                       |
| -------------- | ------- | ---------------------------------------- |
| GPUImageFilter | 单目标输入滤镜 | [参见GPUImage源码阅读（九）](https://www.jianshu.com/p/0f45736e8fde) |

- 多重目标输入滤镜

| 类名                       | 说明       | 解析                                       |
| ------------------------ | -------- | ---------------------------------------- |
| GPUImageTwoInputFilter   | 两个目标输入滤镜 | [参见GPUImage源码阅读（八）](https://www.jianshu.com/p/86e59979beef) |
| GPUImageThreeInputFilter | 三个目标输入滤镜 | [参见GPUImage源码阅读（八）](https://www.jianshu.com/p/86e59979beef) |
| GPUImageFourInputFilter  | 四个目标输入滤镜 | [参见GPUImage源码阅读（八）](https://www.jianshu.com/p/86e59979beef) |

- 组合滤镜

| 类名                     | 说明             | 解析                                       |
| ---------------------- | -------------- | ---------------------------------------- |
| GPUImageFilterGroup    | 自身可参与响应链的组合滤镜  | [参见GPUImage源码阅读（七）](https://www.jianshu.com/p/b2d39abfaa93) |
| GPUImageFilterPipeline | 自身不可参与响应链的组合滤镜 | [参见GPUImage源码阅读（七）](https://www.jianshu.com/p/b2d39abfaa93) |

> ###### 输出

| 类名                    | 说明     | 解析                                       |
| --------------------- | ------ | ---------------------------------------- |
| GPUImageRawDataOutput | 元数据输出  | [参见GPUImage源码阅读（六）](https://www.jianshu.com/p/8b4263d13cf0) |
| GPUImageTextureOutput | 纹理输出   | [参见GPUImage源码阅读（六）](https://www.jianshu.com/p/8b4263d13cf0) |
| GPUImageMovieWriter   | 视频输出   | [参见GPUImage源码阅读（五）](https://www.jianshu.com/p/8f1b7dca9956) |
| GPUImageView          | 图片预览输出 | [参见GPUImage源码阅读（四）](https://www.jianshu.com/p/9b349c04a2dc) |



#### 1 GLProgram

GLProgram专门处理OpenGL ES程序的创建等相关工作, 初始化OpenGL ES环境，编译、链接顶点着色器和片元着色器.

```objective-c
@interface GLProgram : NSObject 
{
    NSMutableArray  *attributes;
    NSMutableArray  *uniforms;
    GLuint          program,
	vertShader, 
	fragShader;	
}

@property(readwrite, nonatomic) BOOL initialized;
@property(readwrite, copy, nonatomic) NSString *vertexShaderLog;
@property(readwrite, copy, nonatomic) NSString *fragmentShaderLog;
@property(readwrite, copy, nonatomic) NSString *programLog;

- (id)initWithVertexShaderString:(NSString *)vShaderString 
            fragmentShaderString:(NSString *)fShaderString;
- (id)initWithVertexShaderString:(NSString *)vShaderString 
          fragmentShaderFilename:(NSString *)fShaderFilename;
- (id)initWithVertexShaderFilename:(NSString *)vShaderFilename 
            fragmentShaderFilename:(NSString *)fShaderFilename;
- (void)addAttribute:(NSString *)attributeName;
- (GLuint)attributeIndex:(NSString *)attributeName;
- (GLuint)uniformIndex:(NSString *)uniformName;
- (BOOL)link;
- (void)use;
- (void)validate;
@end
```

#### 2 GPUImageContext

GPUImageContext类，提供OpenGL ES基本上下文，GPUImage相关处理线程，GLProgram缓存、帧缓存。由于是上下文对象，因此该模块提供的更多是存取、设置相关的方法, 管理着OpenGLES上下文对象，管理着GL程序，管理着帧缓存等GPUImage中的基本组件。

```objective-c
@interface GPUImageContext : NSObject

@property(readonly, nonatomic) dispatch_queue_t contextQueue;
@property(readwrite, retain, nonatomic) GLProgram *currentShaderProgram;
@property(readonly, retain, nonatomic) EAGLContext *context;
@property(readonly) CVOpenGLESTextureCacheRef coreVideoTextureCache;
@property(readonly) GPUImageFramebufferCache *framebufferCache;

+ (void *)contextKey;
+ (GPUImageContext *)sharedImageProcessingContext;
+ (dispatch_queue_t)sharedContextQueue;
+ (GPUImageFramebufferCache *)sharedFramebufferCache;
+ (void)useImageProcessingContext;
- (void)useAsCurrentContext;
+ (void)setActiveShaderProgram:(GLProgram *)shaderProgram;
- (void)setContextShaderProgram:(GLProgram *)shaderProgram;
+ (GLint)maximumTextureSizeForThisDevice;
+ (GLint)maximumTextureUnitsForThisDevice;
+ (GLint)maximumVaryingVectorsForThisDevice;
+ (BOOL)deviceSupportsOpenGLESExtension:(NSString *)extension;
+ (BOOL)deviceSupportsRedTextures;
+ (BOOL)deviceSupportsFramebufferReads;
+ (CGSize)sizeThatFitsWithinATextureForSize:(CGSize)inputSize;

- (void)presentBufferForDisplay;
- (GLProgram *)programForVertexShaderString:(NSString *)vertexShaderString fragmentShaderString:(NSString *)fragmentShaderString;

- (void)useSharegroup:(EAGLSharegroup *)sharegroup;

// Manage fast texture upload
+ (BOOL)supportsFastTextureUpload;

@end
```

#### 3 GPUImageFramebuffer / GPUImageFramebufferCache

GPUImageFramebuffer、GPUImageFramebufferCache 是GPUImage中处理帧缓存相关的类，是各种滤镜的基础。

```objective-c
@interface GPUImageFramebuffer : NSObject

@property(readonly) CGSize size;
@property(readonly) GPUTextureOptions textureOptions;
@property(readonly) GLuint texture;
@property(readonly) BOOL missingFramebuffer;

// Initialization and teardown
- (id)initWithSize:(CGSize)framebufferSize;
- (id)initWithSize:(CGSize)framebufferSize textureOptions:(GPUTextureOptions)fboTextureOptions onlyTexture:(BOOL)onlyGenerateTexture;
- (id)initWithSize:(CGSize)framebufferSize overriddenTexture:(GLuint)inputTexture;

// Usage
- (void)activateFramebuffer;

// Reference counting
- (void)lock;
- (void)unlock;
- (void)clearAllLocks;
- (void)disableReferenceCounting;
- (void)enableReferenceCounting;

// Image capture
- (CGImageRef)newCGImageFromFramebufferContents;
- (void)restoreRenderTarget;

// Raw data bytes
- (void)lockForReading;
- (void)unlockAfterReading;
- (NSUInteger)bytesPerRow;
- (GLubyte *)byteBuffer;
- (CVPixelBufferRef)pixelBuffer;

@end
```

```objective-c
@interface GPUImageFramebufferCache : NSObject

// Framebuffer management
- (GPUImageFramebuffer *)fetchFramebufferForSize:(CGSize)framebufferSize textureOptions:(GPUTextureOptions)textureOptions onlyTexture:(BOOL)onlyTexture;
- (GPUImageFramebuffer *)fetchFramebufferForSize:(CGSize)framebufferSize onlyTexture:(BOOL)onlyTexture;
- (void)returnFramebufferToCache:(GPUImageFramebuffer *)framebuffer;
- (void)purgeAllUnassignedFramebuffers;
- (void)addFramebufferToActiveImageCaptureList:(GPUImageFramebuffer *)framebuffer;
- (void)removeFramebufferFromActiveImageCaptureList:(GPUImageFramebuffer *)framebuffer;

@end
```

#### 4 GPUImageInput / GUPImageOutput

GPUImageInput 是GPUImage中的一个重要的协议，实现这个协议的类表示这个类能接受帧缓存的输入，在响应链中每一个中间节点都能够接受输入经过它的处理之后又能输出给下一个节点。正式这样的过程构成了一个响应链条，这也是叠加滤镜、组合滤镜的基础。

GPUImageInput 协议提供了方法列表，细节由实现的对象实现。GPUImage中实现GPUImageInput的协议的类比较多，常见的有 `GPUImageFilter`、`GPUImageView`、`GPUImageRawDataOutput`、`GPUImageMovieWriter` 等。

```objective-c
@protocol GPUImageInput <NSObject>
- (void)newFrameReadyAtTime:(CMTime)frameTime atIndex:(NSInteger)textureIndex;
- (void)setInputFramebuffer:(GPUImageFramebuffer *)newInputFramebuffer atIndex:(NSInteger)textureIndex;
- (NSInteger)nextAvailableTextureIndex;
- (void)setInputSize:(CGSize)newSize atIndex:(NSInteger)textureIndex;
- (void)setInputRotation:(GPUImageRotationMode)newInputRotation atIndex:(NSInteger)textureIndex;
- (CGSize)maximumOutputSize;
- (void)endProcessing;
- (BOOL)shouldIgnoreUpdatesToThisTarget;
- (BOOL)enabled;
- (BOOL)wantsMonochromeInput;
- (void)setCurrentlyReceivingMonochromeInput:(BOOL)newValue;
@end
```

GPUImageOutput 表示该类能够作为输出，输出的是 `GPUImageFramebuffer` 对象。该类的实现比较简单，主要是实现了一些最基本的方法，这些方法不需要依赖具体细节，细节处理在子类中完成。继承 GPUImageOutput 的类也比较多，比如：`GPUImageFilter`、`GPUImageVideoCamera`、`GPUImageStillCamera`、`GPUImagePicture` 等。

- 基本属性

```objective-c
@interface GPUImageOutput : NSObject
{
    // 输出的帧缓存对象
    GPUImageFramebuffer *outputFramebuffer;
   
   // target列表，target纹理索引列表
    NSMutableArray *targets, *targetTextureIndices;

    // 纹理尺寸
    CGSize inputTextureSize, cachedMaximumOutputSize, forcedMaximumSize;
    BOOL overrideInputSize;
    BOOL allTargetsWantMonochromeData;

    // 设置下一帧提取图片
    BOOL usingNextFrameForImageCapture;
}
// 是否使用mipmaps
@property(readwrite, nonatomic) BOOL shouldSmoothlyScaleOutput;
// 是否忽略处理当前Target
@property(readwrite, nonatomic) BOOL shouldIgnoreUpdatesToThisTarget;
@property(readwrite, nonatomic, retain) GPUImageMovieWriter *audioEncodingTarget;
// 当前忽略处理的Target
@property(readwrite, nonatomic, unsafe_unretained) id<GPUImageInput> targetToIgnoreForUpdates;
// 每帧处理完回调
@property(nonatomic, copy) void(^frameProcessingCompletionBlock)(GPUImageOutput*, CMTime);
// 是否启用渲染目标
@property(nonatomic) BOOL enabled;
// 纹理选项
@property(readwrite, nonatomic) GPUTextureOptions outputTextureOptions;
```

- 方法列表。GPUImageOutput 提供方法主要是以下几种类型：1、帧缓冲对象管理；2、响应链的管理；3、图像提取。

```objective-c
// 设置输入的帧缓冲对象以及纹理索引
- (void)setInputFramebufferForTarget:(id<GPUImageInput>)target atIndex:(NSInteger)inputTextureIndex;
// 输出的帧缓冲对象
- (GPUImageFramebuffer *)framebufferForOutput;
// 删除帧缓冲对象
- (void)removeOutputFramebuffer;
// 通知所有的Target
- (void)notifyTargetsAboutNewOutputTexture;

// 所有的Target列表
- (NSArray*)targets;
// 增加Target
- (void)addTarget:(id<GPUImageInput>)newTarget;
- (void)addTarget:(id<GPUImageInput>)newTarget atTextureLocation:(NSInteger)textureLocation;
// 删除Target
- (void)removeTarget:(id<GPUImageInput>)targetToRemove;
- (void)removeAllTargets;

// 强制按照传入的尺寸处理
- (void)forceProcessingAtSize:(CGSize)frameSize;
- (void)forceProcessingAtSizeRespectingAspectRatio:(CGSize)frameSize;

// 从帧缓冲对象提取CGImage图像
- (void)useNextFrameForImageCapture;
- (CGImageRef)newCGImageFromCurrentlyProcessedOutput;
// 使用静态图片做滤镜纹理
- (CGImageRef)newCGImageByFilteringCGImage:(CGImageRef)imageToFilter;

// 从帧缓冲对象提取UIImage图像
- (UIImage *)imageFromCurrentFramebuffer;
- (UIImage *)imageFromCurrentFramebufferWithOrientation:(UIImageOrientation)imageOrientation;
// 使用静态图片做滤镜纹理
- (UIImage *)imageByFilteringImage:(UIImage *)imageToFilter;
- (CGImageRef)newCGImageByFilteringImage:(UIImage *)imageToFilter;

// 是否提供单色输出
- (BOOL)providesMonochromeOutput;
```

- 帧缓冲对象管理。包含了设置输入的帧缓冲对象，获取输出的帧缓冲对象，以及移除输的帧缓冲对象。

```objective-c
- (void)setInputFramebufferForTarget:(id<GPUImageInput>)target atIndex:(NSInteger)inputTextureIndex;
{
    [target setInputFramebuffer:[self framebufferForOutput] atIndex:inputTextureIndex];
}

- (GPUImageFramebuffer *)framebufferForOutput;
{
    return outputFramebuffer;
}

- (void)removeOutputFramebuffer;
{
    outputFramebuffer = nil;
}
```

- 响应链管理。包含了增加Target，删除Target，处理完成后通知各个Target处理。

```objective-c
- (void)notifyTargetsAboutNewOutputTexture;
{
    for (id<GPUImageInput> currentTarget in targets)
    {
        NSInteger indexOfObject = [targets indexOfObject:currentTarget];
        NSInteger textureIndex = [[targetTextureIndices objectAtIndex:indexOfObject] integerValue];
        
        [self setInputFramebufferForTarget:currentTarget atIndex:textureIndex];
    }
}

- (NSArray*)targets;
{
    return [NSArray arrayWithArray:targets];
}

- (void)addTarget:(id<GPUImageInput>)newTarget;
{
    NSInteger nextAvailableTextureIndex = [newTarget nextAvailableTextureIndex];
    [self addTarget:newTarget atTextureLocation:nextAvailableTextureIndex];
    
    if ([newTarget shouldIgnoreUpdatesToThisTarget])
    {
        _targetToIgnoreForUpdates = newTarget;
    }
}

- (void)addTarget:(id<GPUImageInput>)newTarget atTextureLocation:(NSInteger)textureLocation;
{
    if([targets containsObject:newTarget])
    {
        return;
    }
    
    cachedMaximumOutputSize = CGSizeZero;
    runSynchronouslyOnVideoProcessingQueue(^{
        [self setInputFramebufferForTarget:newTarget atIndex:textureLocation];
        [targets addObject:newTarget];
        [targetTextureIndices addObject:[NSNumber numberWithInteger:textureLocation]];
        
        allTargetsWantMonochromeData = allTargetsWantMonochromeData && [newTarget wantsMonochromeInput];
    });
}

- (void)removeTarget:(id<GPUImageInput>)targetToRemove;
{
    if(![targets containsObject:targetToRemove])
    {
        return;
    }
    
    if (_targetToIgnoreForUpdates == targetToRemove)
    {
        _targetToIgnoreForUpdates = nil;
    }
    
    cachedMaximumOutputSize = CGSizeZero;
    
    NSInteger indexOfObject = [targets indexOfObject:targetToRemove];
    NSInteger textureIndexOfTarget = [[targetTextureIndices objectAtIndex:indexOfObject] integerValue];

    runSynchronouslyOnVideoProcessingQueue(^{
        [targetToRemove setInputSize:CGSizeZero atIndex:textureIndexOfTarget];
        [targetToRemove setInputRotation:kGPUImageNoRotation atIndex:textureIndexOfTarget];

        [targetTextureIndices removeObjectAtIndex:indexOfObject];
        [targets removeObject:targetToRemove];
        [targetToRemove endProcessing];
    });
}

- (void)removeAllTargets;
{
    cachedMaximumOutputSize = CGSizeZero;
    runSynchronouslyOnVideoProcessingQueue(^{
        for (id<GPUImageInput> targetToRemove in targets)
        {
            NSInteger indexOfObject = [targets indexOfObject:targetToRemove];
            NSInteger textureIndexOfTarget = [[targetTextureIndices objectAtIndex:indexOfObject] integerValue];
            
            [targetToRemove setInputSize:CGSizeZero atIndex:textureIndexOfTarget];
            [targetToRemove setInputRotation:kGPUImageNoRotation atIndex:textureIndexOfTarget];
        }
        [targets removeAllObjects];
        [targetTextureIndices removeAllObjects];
        
        allTargetsWantMonochromeData = YES;
    });
}
```

- 提取图像。包含了提取得到 CGImage 和 UIImage 。

```objective-c
- (void)useNextFrameForImageCapture;
{

}

- (CGImageRef)newCGImageFromCurrentlyProcessedOutput;
{
    return nil;
}

- (CGImageRef)newCGImageByFilteringCGImage:(CGImageRef)imageToFilter;
{
    GPUImagePicture *stillImageSource = [[GPUImagePicture alloc] initWithCGImage:imageToFilter];
    
    [self useNextFrameForImageCapture];
    [stillImageSource addTarget:(id<GPUImageInput>)self];
    [stillImageSource processImage];
    
    CGImageRef processedImage = [self newCGImageFromCurrentlyProcessedOutput];
    
    [stillImageSource removeTarget:(id<GPUImageInput>)self];
    return processedImage;
}

- (UIImage *)imageFromCurrentFramebuffer;
{
    UIDeviceOrientation deviceOrientation = [[UIDevice currentDevice] orientation];
    UIImageOrientation imageOrientation = UIImageOrientationLeft;
    switch (deviceOrientation)
    {
        case UIDeviceOrientationPortrait:
            imageOrientation = UIImageOrientationUp;
            break;
        case UIDeviceOrientationPortraitUpsideDown:
            imageOrientation = UIImageOrientationDown;
            break;
        case UIDeviceOrientationLandscapeLeft:
            imageOrientation = UIImageOrientationLeft;
            break;
        case UIDeviceOrientationLandscapeRight:
            imageOrientation = UIImageOrientationRight;
            break;
        default:
            imageOrientation = UIImageOrientationUp;
            break;
    }
    
    return [self imageFromCurrentFramebufferWithOrientation:imageOrientation];
}

- (UIImage *)imageFromCurrentFramebufferWithOrientation:(UIImageOrientation)imageOrientation;
{
    CGImageRef cgImageFromBytes = [self newCGImageFromCurrentlyProcessedOutput];
    UIImage *finalImage = [UIImage imageWithCGImage:cgImageFromBytes scale:1.0 orientation:imageOrientation];
    CGImageRelease(cgImageFromBytes);
    
    return finalImage;
}

- (UIImage *)imageByFilteringImage:(UIImage *)imageToFilter;
{
    CGImageRef image = [self newCGImageByFilteringCGImage:[imageToFilter CGImage]];
    UIImage *processedImage = [UIImage imageWithCGImage:image scale:[imageToFilter scale] orientation:[imageToFilter imageOrientation]];
    CGImageRelease(image);
    return processedImage;
}
```

**总结**:  [**GPUImageInput**](https://link.jianshu.com?t=https://github.com/BradLarson/GPUImage/blob/master/framework/Source/iOS/GPUImageContext.h) 、[**GPUImageOutput**](https://link.jianshu.com?t=https://github.com/BradLarson/GPUImage/blob/master/framework/Source/GPUImageOutput.h) 是构成GPUImage响应链的基础。如果一个类实现了 GPUImageInput 协议我们可以知道它能够接收帧缓存对象的输入，如果继承了 GPUImageOutput 类，我们可以知道它能够输出帧缓存对象。如果两个都具备，则表明既能处理输入又可以输出，比如 `GPUImageFilter` ，而这就是响应链的基本要求。

#### 5 GPUImagePicture / GPUImageView / GPUImageUIElement

这三个类与iOS中的图片加载、图片显示、UI渲染相关。在使用GPUImage框架的时候，涉及到对图片进行滤镜处理和显示的时候，基本都会用到这几个类。

> **GPUImagePicture**是GPUImage框架中处理与图片相关的类，它的主要作用是将UIImage或CGImage转化为纹理对象。GPUImagePicture**继承自GPUImageOutput**，从而可以知道它能够作为输出，由于它没有实现GPUImageInput协议，不能处理输入。因此，常常作为**响应链源**。

>**GPUImageView**是GPUImage框架中显示图片相关的类。GPUImageView**实现了GPUImageInput协议**，从而可以知道它能够接受GPUImageFramebuffer的输入。因此，常常作为**响应链的终端节点**，用于显示处理后的帧缓存。

>**GPUImageUIElement**与GPUImagePicture类似可以作为响应链源。与GPUImagePicture不同的是，它的数据不是来自图片，而是来自于UIView或CALayer的渲染结果，类似于对UIView或CALayer截图。GPUImageUIElement继承自GPUImageOutput，从而可以知道它**能够作为输出**，由于它没有实现GPUImageInput协议，不能处理输入。

#### 6 GPUImageVideoCamera / GPUImageStillCamera / GPUImageMovieWriter / GPUImageMovie

这几个类在处理相机、音视频的时候非常有用。处理数据源来自相机、音视频文件的情况。

#### 7 GPUImageRawDataInput / GPUImageRawDataOutput / GPUImageTextureInput / GPUImageTextureOutput

如果需要使用原始图片数据和纹理的时候可以考虑使用它们。RGBA原始数据（包括：RGB、RGBA、BGRA、LUMINANCE）和纹理对象。

#### 8 GPUImageFilterPipeline / GPUImageFilterGroup

这两个类都和组合滤镜相关，但是它们又有不同的地方。GPUImageFilterPipeline 继承自NSObject，也没有实现其它协议，而GPUImageFilterGroup继承自GPUImageOutput 实现了GPUImageInput协议。因此，最大的区别就是GPUImageFilterGroup自身可以作为响应链的一员，而GPUImageFilterPipeline则不能。

######8.1 GPUImageFilterPipeline 

GPUImageFilterPipeline 继承自NSObject，它的主要作用是管理滤镜链，自身不能参与响应链中。可以用来构建简单的滤镜组合。如果滤镜比较复杂或是涉及到多个纹理的处理，GPUImageFilterGroup则是更好的选择。

```objective-c
@interface GPUImageFilterPipeline : NSObject
{
    NSString *stringValue;
}
// filter数组
@property (strong) NSMutableArray *filters;
// 输入对象
@property (strong) GPUImageOutput *input;
// 输出对象
@property (strong) id <GPUImageInput> output;

- (id) initWithOrderedFilters:(NSArray*) filters input:(GPUImageOutput*)input output:(id <GPUImageInput>)output;
- (id) initWithConfiguration:(NSDictionary*) configuration input:(GPUImageOutput*)input output:(id <GPUImageInput>)output;
- (id) initWithConfigurationFile:(NSURL*) configuration input:(GPUImageOutput*)input output:(id <GPUImageInput>)output;

// filter的增加、删除、替换
- (void) addFilter:(GPUImageOutput<GPUImageInput> *)filter;
- (void) addFilter:(GPUImageOutput<GPUImageInput> *)filter atIndex:(NSUInteger)insertIndex;
- (void) replaceFilterAtIndex:(NSUInteger)index withFilter:(GPUImageOutput<GPUImageInput> *)filter;
- (void) replaceAllFilters:(NSArray *) newFilters;
- (void) removeFilter:(GPUImageOutput<GPUImageInput> *)filter;
- (void) removeFilterAtIndex:(NSUInteger)index;
- (void) removeAllFilters;

// 由final filter生成图片
- (UIImage *) currentFilteredFrame;
- (UIImage *) currentFilteredFrameWithOrientation:(UIImageOrientation)imageOrientation;
- (CGImageRef) newCGImageFromCurrentFilteredFrame;

@end

// 在特定位置增加filter
- (void)addFilter:(GPUImageOutput<GPUImageInput> *)filter atIndex:(NSUInteger)insertIndex {
    [self.filters insertObject:filter atIndex:insertIndex];
    [self _refreshFilters];
}

// 在末尾增加filter
- (void)addFilter:(GPUImageOutput<GPUImageInput> *)filter {
    [self.filters addObject:filter];
    [self _refreshFilters];
}

// 替换filter
- (void)replaceFilterAtIndex:(NSUInteger)index withFilter:(GPUImageOutput<GPUImageInput> *)filter {
    [self.filters replaceObjectAtIndex:index withObject:filter];
    [self _refreshFilters];
}

// 删除filter
- (void) removeFilter:(GPUImageOutput<GPUImageInput> *)filter;
{
    [self.filters removeObject:filter];
    [self _refreshFilters];
}

// 删除特定索引的filter
- (void)removeFilterAtIndex:(NSUInteger)index {
    [self.filters removeObjectAtIndex:index];
    [self _refreshFilters];
}

// 删除所有filter
- (void)removeAllFilters {
    [self.filters removeAllObjects];
    [self _refreshFilters];
}

// 替换所有filter
- (void)replaceAllFilters:(NSArray *)newFilters {
    self.filters = [NSMutableArray arrayWithArray:newFilters];
    [self _refreshFilters];
}

// 更新响应链
- (void)_refreshFilters {
    // 将input作为响应链源
    id prevFilter = self.input;
    GPUImageOutput<GPUImageInput> *theFilter = nil;
    
    // 循环添加
    for (int i = 0; i < [self.filters count]; i++) {
        theFilter = [self.filters objectAtIndex:i];
        [prevFilter removeAllTargets];
        [prevFilter addTarget:theFilter];
        prevFilter = theFilter;
    }
    
    [prevFilter removeAllTargets];
    
    // 最后将output加入响应链  
    if (self.output != nil) {
        [prevFilter addTarget:self.output];
    }
}

// 由final filter生成图像
- (UIImage *)currentFilteredFrame {
    return [(GPUImageOutput<GPUImageInput> *)[_filters lastObject] imageFromCurrentFramebuffer];
}

// 根据imageOrientation生成图像
- (UIImage *)currentFilteredFrameWithOrientation:(UIImageOrientation)imageOrientation {
  return [(GPUImageOutput<GPUImageInput> *)[_filters lastObject] imageFromCurrentFramebufferWithOrientation:imageOrientation];
}

// 由final filter生成图像
- (CGImageRef)newCGImageFromCurrentFilteredFrame {
    return [(GPUImageOutput<GPUImageInput> *)[_filters lastObject] newCGImageFromCurrentlyProcessedOutput];
}
```

###### 8.2 GPUImageFilterGroup

GPUImageFilterGroup继承自GPUImageOutput ，实现了GPUImageInput协议。因此，可以自身可以作为独立的滤镜参与响应链中。相比GPUImageFilterPipeline，GPUImageFilterGroup功能更强大。

```objective-c
@interface GPUImageFilterGroup : GPUImageOutput <GPUImageInput>
{
    NSMutableArray *filters;
    BOOL isEndProcessing;
}

@property(readwrite, nonatomic, strong) GPUImageOutput<GPUImageInput> *terminalFilter;
@property(readwrite, nonatomic, strong) NSArray *initialFilters;
@property(readwrite, nonatomic, strong) GPUImageOutput<GPUImageInput> *inputFilterToIgnoreForUpdates; 

// Filter management
- (void)addFilter:(GPUImageOutput<GPUImageInput> *)newFilter;
- (GPUImageOutput<GPUImageInput> *)filterAtIndex:(NSUInteger)filterIndex;
- (NSUInteger)filterCount;

@end
```

**总结:** GPUImageFilterPipeline、GPUImageFilterGroup 都可用于组合滤镜，GPUImageFilterPipeline相对比较简单，可定制程度较低，而GPUImageFilterGroup则功能比较强大，可定制程度高。在选用的时候可以根据自己的需求，选用不同的滤镜组合。

#### 9 GPUImageFilter

GPUImageFilter 是GPUImage中很重要、很基础的类，它可以处理帧缓存对象的输入输出，但是对纹理并不添加任何特效，也就是说只是简单的让纹理通过。它更多的是作为其它滤镜的基类，一些具体的滤镜由它的子类去完成。同时它也只能处理单个帧缓存对象的输入，处理多个帧缓存对象的输入也是由它的子类去完成。

GPUImageFilter 本身并不实现相关的滤镜特效，只是简单的输出输入的纹理样式。GPUImageFilter 更多的是作为其它滤镜的基类，它提供了许多最基础的接口，以及控制了整个滤镜链的基本流程。GPUImageFilter 继承自 GPUImageOutput 实现了 GPUImageInput 协议，可以将输入的纹理经过相关处理后输出，从而对纹理应用相关特效。在一个响应链中可以有多个 GPUImageFilter，从而实现了叠加滤镜的效果。

```objective-c
@interface GPUImageFilter : GPUImageOutput <GPUImageInput>
{
    GPUImageFramebuffer *firstInputFramebuffer;
    
    GLProgram *filterProgram;
    GLint filterPositionAttribute, filterTextureCoordinateAttribute;
    GLint filterInputTextureUniform;
    GLfloat backgroundColorRed, backgroundColorGreen, backgroundColorBlue, backgroundColorAlpha;
    
    BOOL isEndProcessing;

    CGSize currentFilterSize;
    GPUImageRotationMode inputRotation;
    
    BOOL currentlyReceivingMonochromeInput;
    
    NSMutableDictionary *uniformStateRestorationBlocks;
    dispatch_semaphore_t imageCaptureSemaphore;
}

@property(readonly) CVPixelBufferRef renderTarget;
@property(readwrite, nonatomic) BOOL preventRendering;
@property(readwrite, nonatomic) BOOL currentlyReceivingMonochromeInput;

/// @name Initialization and teardown

/**
 Initialize with vertex and fragment shaders
 
 You make take advantage of the SHADER_STRING macro to write your shaders in-line.
 @param vertexShaderString Source code of the vertex shader to use
 @param fragmentShaderString Source code of the fragment shader to use
 */
- (id)initWithVertexShaderFromString:(NSString *)vertexShaderString fragmentShaderFromString:(NSString *)fragmentShaderString;

/**
 Initialize with a fragment shader
 
 You may take advantage of the SHADER_STRING macro to write your shader in-line.
 @param fragmentShaderString Source code of fragment shader to use
 */
- (id)initWithFragmentShaderFromString:(NSString *)fragmentShaderString;
/**
 Initialize with a fragment shader
 @param fragmentShaderFilename Filename of fragment shader to load
 */
- (id)initWithFragmentShaderFromFile:(NSString *)fragmentShaderFilename;
- (void)initializeAttributes;
- (void)setupFilterForSize:(CGSize)filterFrameSize;
- (CGSize)rotatedSize:(CGSize)sizeToRotate forIndex:(NSInteger)textureIndex;
- (CGPoint)rotatedPoint:(CGPoint)pointToRotate forRotation:(GPUImageRotationMode)rotation;

/// @name Managing the display FBOs
/** Size of the frame buffer object
 */
- (CGSize)sizeOfFBO;

/// @name Rendering
+ (const GLfloat *)textureCoordinatesForRotation:(GPUImageRotationMode)rotationMode;
- (void)renderToTextureWithVertices:(const GLfloat *)vertices textureCoordinates:(const GLfloat *)textureCoordinates;
- (void)informTargetsAboutNewFrameAtTime:(CMTime)frameTime;
- (CGSize)outputFrameSize;

/// @name Input parameters
- (void)setBackgroundColorRed:(GLfloat)redComponent green:(GLfloat)greenComponent blue:(GLfloat)blueComponent alpha:(GLfloat)alphaComponent;
- (void)setInteger:(GLint)newInteger forUniformName:(NSString *)uniformName;
- (void)setFloat:(GLfloat)newFloat forUniformName:(NSString *)uniformName;
- (void)setSize:(CGSize)newSize forUniformName:(NSString *)uniformName;
- (void)setPoint:(CGPoint)newPoint forUniformName:(NSString *)uniformName;
- (void)setFloatVec3:(GPUVector3)newVec3 forUniformName:(NSString *)uniformName;
- (void)setFloatVec4:(GPUVector4)newVec4 forUniform:(NSString *)uniformName;
- (void)setFloatArray:(GLfloat *)array length:(GLsizei)count forUniform:(NSString*)uniformName;

- (void)setMatrix3f:(GPUMatrix3x3)matrix forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setMatrix4f:(GPUMatrix4x4)matrix forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setFloat:(GLfloat)floatValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setPoint:(CGPoint)pointValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setSize:(CGSize)sizeValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setVec3:(GPUVector3)vectorValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setVec4:(GPUVector4)vectorValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setFloatArray:(GLfloat *)arrayValue length:(GLsizei)arrayLength forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setInteger:(GLint)intValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;

- (void)setAndExecuteUniformStateCallbackAtIndex:(GLint)uniform forProgram:(GLProgram *)shaderProgram toBlock:(dispatch_block_t)uniformStateBlock;
- (void)setUniformsForProgramAtIndex:(NSUInteger)programIndex;

@end
```

- 矩阵

在 GPUImage 中主要用到了3维向量、4维向量、4x4矩阵、3x3矩阵，对应OpenGL中的vec3、vec4、mat4、mat3。之所以使用这些向量、矩阵，是为了方便向着色器传值。在 GPUImageFilter 中定义了一组传值的接口，在需要向着色器传值的时候很方便。

- 着色器

在滤镜中着色器程序是很重要的，它决定了滤镜的表现效果。在 GPUImageFilter 中的着色器程序比较简单，只是简单的进行纹理采样，并没有对像素数据进行相关操作。在自定义相关滤镜的时候，我们通常改变片段着色器就行了，如果涉及多个纹理输入，可以使用之前介绍的多重输入滤镜（也是GPUImageFilter的子类，但扩展了帧缓存的输入）。以下是 GPUImageFilter 的相关着色器。

```objective-c
NSString *const kGPUImageVertexShaderString = SHADER_STRING
(
 attribute vec4 position;
 attribute vec4 inputTextureCoordinate;
 
 varying vec2 textureCoordinate;
 
 void main()
 {
     gl_Position = position;
     textureCoordinate = inputTextureCoordinate.xy;
 }
 );

#if TARGET_IPHONE_SIMULATOR || TARGET_OS_IPHONE

NSString *const kGPUImagePassthroughFragmentShaderString = SHADER_STRING
(
 varying highp vec2 textureCoordinate;
 
 uniform sampler2D inputImageTexture;
 
 void main()
 {
     gl_FragColor = texture2D(inputImageTexture, textureCoordinate);
 }
);

#else

NSString *const kGPUImagePassthroughFragmentShaderString = SHADER_STRING
(
 varying vec2 textureCoordinate;
 
 uniform sampler2D inputImageTexture;
 
 void main()
 {
     gl_FragColor = texture2D(inputImageTexture, textureCoordinate);
 }
);
#endif
```

- 实例变量

GPUImageFilter 中有两个比较重要的实例变量 firstInputFramebuffer、filterProgram。firstInputFramebuffer 表示输入帧缓存对象，filterProgram 表示GL程序。

```objective-c
@interface GPUImageFilter : GPUImageOutput <GPUImageInput>
{
    // 输入帧缓存对象
    GPUImageFramebuffer *firstInputFramebuffer;
    // GL程序
    GLProgram *filterProgram;
    // 属性变量
    GLint filterPositionAttribute, filterTextureCoordinateAttribute;
    // 纹理统一变量
    GLint filterInputTextureUniform;
    // GL清屏颜色
    GLfloat backgroundColorRed, backgroundColorGreen, backgroundColorBlue, backgroundColorAlpha;
    // 结束处理操作
    BOOL isEndProcessing;
   
    CGSize currentFilterSize;
    // 屏幕旋转方向
    GPUImageRotationMode inputRotation;
    
    BOOL currentlyReceivingMonochromeInput;
    
    // 保存RestorationBlocks的字典
    NSMutableDictionary *uniformStateRestorationBlocks;
    // 信号量
    dispatch_semaphore_t imageCaptureSemaphore;
}
```

- 构造方法

GPUImageFilter 构造方法需要我们传入顶点着色器和片段着色器就，当然我们一般只需要传入片段着色器即可。初始化的过程可以概括为这几个步骤：1、初始化相关实例变量；2、初始化GL上下文对象；3、初始化GL程序；4、创建GL程序；5、获取GL相关变量。

```objective-c
- (id)initWithVertexShaderFromString:(NSString *)vertexShaderString fragmentShaderFromString:(NSString *)fragmentShaderString;
- (id)initWithFragmentShaderFromString:(NSString *)fragmentShaderString;
- (id)initWithFragmentShaderFromFile:(NSString *)fragmentShaderFilename;

/******************* 方法实现 ************************************/
- (id)initWithVertexShaderFromString:(NSString *)vertexShaderString fragmentShaderFromString:(NSString *)fragmentShaderString;
{
    if (!(self = [super init]))
    {
        return nil;
    }
    
    // 初始化相关实例变量
    uniformStateRestorationBlocks = [NSMutableDictionary dictionaryWithCapacity:10];
    _preventRendering = NO;
    currentlyReceivingMonochromeInput = NO;
    inputRotation = kGPUImageNoRotation;
    backgroundColorRed = 0.0;
    backgroundColorGreen = 0.0;
    backgroundColorBlue = 0.0;
    backgroundColorAlpha = 0.0;
    imageCaptureSemaphore = dispatch_semaphore_create(0);
    dispatch_semaphore_signal(imageCaptureSemaphore);

    runSynchronouslyOnVideoProcessingQueue(^{
        // 初始化GL上下文对象
        [GPUImageContext useImageProcessingContext];
        // 创建GL程序
        filterProgram = [[GPUImageContext sharedImageProcessingContext] programForVertexShaderString:vertexShaderString fragmentShaderString:fragmentShaderString];
        
        if (!filterProgram.initialized)
        {
            // 初始化属性变量
            [self initializeAttributes];
            
            // 链接着色器程序
            if (![filterProgram link])
            {
                // 输出错误日志
                NSString *progLog = [filterProgram programLog];
                NSLog(@"Program link log: %@", progLog);
                NSString *fragLog = [filterProgram fragmentShaderLog];
                NSLog(@"Fragment shader compile log: %@", fragLog);
                NSString *vertLog = [filterProgram vertexShaderLog];
                NSLog(@"Vertex shader compile log: %@", vertLog);
                filterProgram = nil;
                NSAssert(NO, @"Filter shader link failed");
            }
        }
        // 获取顶点属性变量
        filterPositionAttribute = [filterProgram attributeIndex:@"position"];
        // 获取纹理坐标属性变量
        filterTextureCoordinateAttribute = [filterProgram attributeIndex:@"inputTextureCoordinate"];
        // 获取纹理统一变量
        filterInputTextureUniform = [filterProgram uniformIndex:@"inputImageTexture"]; // This does assume a name of "inputImageTexture" for the fragment shader
         // 使用当前GL程序
        [GPUImageContext setActiveShaderProgram:filterProgram];
        // 启用顶点属性数组
        glEnableVertexAttribArray(filterPositionAttribute);
        glEnableVertexAttribArray(filterTextureCoordinateAttribute);    
    });
    
    return self;
}

- (id)initWithFragmentShaderFromString:(NSString *)fragmentShaderString;
{
    if (!(self = [self initWithVertexShaderFromString:kGPUImageVertexShaderString fragmentShaderFromString:fragmentShaderString]))
    {
        return nil;
    }
    
    return self;
}

- (id)initWithFragmentShaderFromFile:(NSString *)fragmentShaderFilename;
{
    NSString *fragmentShaderPathname = [[NSBundle mainBundle] pathForResource:fragmentShaderFilename ofType:@"fsh"];
    NSString *fragmentShaderString = [NSString stringWithContentsOfFile:fragmentShaderPathname encoding:NSUTF8StringEncoding error:nil];

    if (!(self = [self initWithFragmentShaderFromString:fragmentShaderString]))
    {
        return nil;
    }
    
    return self;
}
```

- 其它方法

GPUImageFilter 的方法中，为着色器传值的方法比较多，这是因为着色器能接受不同类型的值，如：GLint、GLfloat、vec2、vec3、mat3 等。在这些方法中有三个比较重要的方法 `- (void)newFrameReadyAtTime:(CMTime)frameTime atIndex:(NSInteger)textureIndex;` 和 `- (void)renderToTextureWithVertices:(const GLfloat *)vertices textureCoordinates:(const GLfloat *)textureCoordinates;` 和 `- (void)informTargetsAboutNewFrameAtTime:(CMTime)frameTime;` 这三个方法和响应链密切相关。GPUImageFilter 会将接收到的帧缓存对象经过特定的片段着色器绘制到即将输出的帧缓存对象中，然后将自己输出的帧缓存对象传给所有Targets并通知它们进行处理。方法被调用的顺序：

> 1、生成新的帧缓存对象
> `- (void)newFrameReadyAtTime:(CMTime)frameTime atIndex:(NSInteger)textureIndex;`；
> 2、进行GL绘制
> `- (void)renderToTextureWithVertices:(const GLfloat *)vertices textureCoordinates:(const GLfloat *)textureCoordinates;`
> 3、绘制完成通知所有的target处理
> `- (void)informTargetsAboutNewFrameAtTime:(CMTime)frameTime;`

接下来主要看这几个方法。

```objective-c
// 变换方法
- (void)setupFilterForSize:(CGSize)filterFrameSize;
- (CGSize)rotatedSize:(CGSize)sizeToRotate forIndex:(NSInteger)textureIndex;
- (CGPoint)rotatedPoint:(CGPoint)pointToRotate forRotation:(GPUImageRotationMode)rotation;

// 查询方法
- (CGSize)sizeOfFBO;
+ (const GLfloat *)textureCoordinatesForRotation:(GPUImageRotationMode)rotationMode;
- (CGSize)outputFrameSize;

// 渲染方法
- (void)renderToTextureWithVertices:(const GLfloat *)vertices textureCoordinates:(const GLfloat *)textureCoordinates;
- (void)informTargetsAboutNewFrameAtTime:(CMTime)frameTime;

// 设置清屏颜色
- (void)setBackgroundColorRed:(GLfloat)redComponent green:(GLfloat)greenComponent blue:(GLfloat)blueComponent alpha:(GLfloat)alphaComponent;

// 传值方法
- (void)setInteger:(GLint)newInteger forUniformName:(NSString *)uniformName;
- (void)setFloat:(GLfloat)newFloat forUniformName:(NSString *)uniformName;
- (void)setSize:(CGSize)newSize forUniformName:(NSString *)uniformName;
- (void)setPoint:(CGPoint)newPoint forUniformName:(NSString *)uniformName;
- (void)setFloatVec3:(GPUVector3)newVec3 forUniformName:(NSString *)uniformName;
- (void)setFloatVec4:(GPUVector4)newVec4 forUniform:(NSString *)uniformName;
- (void)setFloatArray:(GLfloat *)array length:(GLsizei)count forUniform:(NSString*)uniformName;
- (void)setMatrix3f:(GPUMatrix3x3)matrix forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setMatrix4f:(GPUMatrix4x4)matrix forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setFloat:(GLfloat)floatValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setPoint:(CGPoint)pointValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setSize:(CGSize)sizeValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setVec3:(GPUVector3)vectorValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setVec4:(GPUVector4)vectorValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setFloatArray:(GLfloat *)arrayValue length:(GLsizei)arrayLength forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setInteger:(GLint)intValue forUniform:(GLint)uniform program:(GLProgram *)shaderProgram;
- (void)setAndExecuteUniformStateCallbackAtIndex:(GLint)uniform forProgram:(GLProgram *)shaderProgram toBlock:(dispatch_block_t)uniformStateBlock;
- (void)setUniformsForProgramAtIndex:(NSUInteger)programIndex;


/******************* 方法实现 ************************************/
// 根据旋转方向获取纹理坐标
+ (const GLfloat *)textureCoordinatesForRotation:(GPUImageRotationMode)rotationMode;
{
    static const GLfloat noRotationTextureCoordinates[] = {
        0.0f, 0.0f,
        1.0f, 0.0f,
        0.0f, 1.0f,
        1.0f, 1.0f,
    };
    
    static const GLfloat rotateLeftTextureCoordinates[] = {
        1.0f, 0.0f,
        1.0f, 1.0f,
        0.0f, 0.0f,
        0.0f, 1.0f,
    };
    
    static const GLfloat rotateRightTextureCoordinates[] = {
        0.0f, 1.0f,
        0.0f, 0.0f,
        1.0f, 1.0f,
        1.0f, 0.0f,
    };
    
    static const GLfloat verticalFlipTextureCoordinates[] = {
        0.0f, 1.0f,
        1.0f, 1.0f,
        0.0f,  0.0f,
        1.0f,  0.0f,
    };
    
    static const GLfloat horizontalFlipTextureCoordinates[] = {
        1.0f, 0.0f,
        0.0f, 0.0f,
        1.0f,  1.0f,
        0.0f,  1.0f,
    };
    
    static const GLfloat rotateRightVerticalFlipTextureCoordinates[] = {
        0.0f, 0.0f,
        0.0f, 1.0f,
        1.0f, 0.0f,
        1.0f, 1.0f,
    };

    static const GLfloat rotateRightHorizontalFlipTextureCoordinates[] = {
        1.0f, 1.0f,
        1.0f, 0.0f,
        0.0f, 1.0f,
        0.0f, 0.0f,
    };

    static const GLfloat rotate180TextureCoordinates[] = {
        1.0f, 1.0f,
        0.0f, 1.0f,
        1.0f, 0.0f,
        0.0f, 0.0f,
    };

    switch(rotationMode)
    {
        case kGPUImageNoRotation: return noRotationTextureCoordinates;
        case kGPUImageRotateLeft: return rotateLeftTextureCoordinates;
        case kGPUImageRotateRight: return rotateRightTextureCoordinates;
        case kGPUImageFlipVertical: return verticalFlipTextureCoordinates;
        case kGPUImageFlipHorizonal: return horizontalFlipTextureCoordinates;
        case kGPUImageRotateRightFlipVertical: return rotateRightVerticalFlipTextureCoordinates;
        case kGPUImageRotateRightFlipHorizontal: return rotateRightHorizontalFlipTextureCoordinates;
        case kGPUImageRotate180: return rotate180TextureCoordinates;
    }
}

// 产生新的帧缓存
- (void)newFrameReadyAtTime:(CMTime)frameTime atIndex:(NSInteger)textureIndex;
{
    static const GLfloat imageVertices[] = {
        -1.0f, -1.0f,
        1.0f, -1.0f,
        -1.0f,  1.0f,
        1.0f,  1.0f,
    };
    
    // 先渲染到帧缓存
    [self renderToTextureWithVertices:imageVertices textureCoordinates:[[self class] textureCoordinatesForRotation:inputRotation]];
  
    // 通知所有的Targets
    [self informTargetsAboutNewFrameAtTime:frameTime];
}

// 渲染到帧缓存
- (void)renderToTextureWithVertices:(const GLfloat *)vertices textureCoordinates:(const GLfloat *)textureCoordinates;
{
    if (self.preventRendering)
    {
        [firstInputFramebuffer unlock];
        return;
    }
    
    [GPUImageContext setActiveShaderProgram:filterProgram];

    outputFramebuffer = [[GPUImageContext sharedFramebufferCache] fetchFramebufferForSize:[self sizeOfFBO] textureOptions:self.outputTextureOptions onlyTexture:NO];
    [outputFramebuffer activateFramebuffer];
    if (usingNextFrameForImageCapture)
    {
        [outputFramebuffer lock];
    }

    [self setUniformsForProgramAtIndex:0];
    
    // GL绘制
    glClearColor(backgroundColorRed, backgroundColorGreen, backgroundColorBlue, backgroundColorAlpha);
    glClear(GL_COLOR_BUFFER_BIT);

    glActiveTexture(GL_TEXTURE2);
    glBindTexture(GL_TEXTURE_2D, [firstInputFramebuffer texture]);
    
    glUniform1i(filterInputTextureUniform, 2);  

    glVertexAttribPointer(filterPositionAttribute, 2, GL_FLOAT, 0, 0, vertices);
    glVertexAttribPointer(filterTextureCoordinateAttribute, 2, GL_FLOAT, 0, 0, textureCoordinates);
    
    glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
    
    // 解锁输入帧缓存对象
    [firstInputFramebuffer unlock];
    
    // 需要等待绘制完成才去生成图像
    if (usingNextFrameForImageCapture)
    {
        // 发送渲染完成信号
        dispatch_semaphore_signal(imageCaptureSemaphore);
    }
}

// 通知所有的Targets
- (void)informTargetsAboutNewFrameAtTime:(CMTime)frameTime;
{
    if (self.frameProcessingCompletionBlock != NULL)
    {
        self.frameProcessingCompletionBlock(self, frameTime);
    }
    
    // 传递帧缓存给所有target
    for (id<GPUImageInput> currentTarget in targets)
    {
        if (currentTarget != self.targetToIgnoreForUpdates)
        {
            NSInteger indexOfObject = [targets indexOfObject:currentTarget];
            NSInteger textureIndex = [[targetTextureIndices objectAtIndex:indexOfObject] integerValue];

            [self setInputFramebufferForTarget:currentTarget atIndex:textureIndex];
            [currentTarget setInputSize:[self outputFrameSize] atIndex:textureIndex];
        }
    }
    
    // Release our hold so it can return to the cache immediately upon processing
    [[self framebufferForOutput] unlock];
    
    if (usingNextFrameForImageCapture)
    {
//        usingNextFrameForImageCapture = NO;
    }
    else
    {
        [self removeOutputFramebuffer];
    }    
    
    // 通知所有targets产生新的帧缓存
    for (id<GPUImageInput> currentTarget in targets)
    {
        if (currentTarget != self.targetToIgnoreForUpdates)
        {
            NSInteger indexOfObject = [targets indexOfObject:currentTarget];
            NSInteger textureIndex = [[targetTextureIndices objectAtIndex:indexOfObject] integerValue];
            // 让所有target生成新的帧缓存
            [currentTarget newFrameReadyAtTime:frameTime atIndex:textureIndex];
        }
    }
}

// 需要生成图片则先消耗信号量，确保生成图片的时候GL绘制已经完成
- (void)useNextFrameForImageCapture;
{
    usingNextFrameForImageCapture = YES;

    // 消耗信号量
    if (dispatch_semaphore_wait(imageCaptureSemaphore, DISPATCH_TIME_NOW) != 0)
    {
        return;
    }
}

// 等待渲染完成信号，如果接收到完成信号则生成图片
- (CGImageRef)newCGImageFromCurrentlyProcessedOutput
{
    // Give it three seconds to process, then abort if they forgot to set up the image capture properly
    double timeoutForImageCapture = 3.0;
    dispatch_time_t convertedTimeout = dispatch_time(DISPATCH_TIME_NOW, timeoutForImageCapture * NSEC_PER_SEC);

    // 等待GL绘制完成，直到超时
    if (dispatch_semaphore_wait(imageCaptureSemaphore, convertedTimeout) != 0)
    {
        return NULL;
    }
  
    // GL渲染完成且等待未超时则生成CGImage
    GPUImageFramebuffer* framebuffer = [self framebufferForOutput];
    
    usingNextFrameForImageCapture = NO;
    dispatch_semaphore_signal(imageCaptureSemaphore);
    
    CGImageRef image = [framebuffer newCGImageFromFramebufferContents];
    return image;
}
```

#### 10 实现过程

> 实现自定义滤镜特效。

1、新建QMFishEyeFilter，并继承自 GPUImageFilter。

```objective-c
#import <GPUImage.h>

@interface QMFishEyeFilter : GPUImageFilter

@property (nonatomic, assign) GLfloat radius;

- (instancetype)init;

@end
```

2、重写 `- (instancetype)init;` 方法。

```objective-c
- (instancetype)init
{
    if (self = [super initWithFragmentShaderFromString:kQMFishEyeFilterFragmentShaderString]) {
        
        radiusUniform = [filterProgram uniformIndex:@"radius"];
        self.radius = 0.5;
        
        [self setBackgroundColorRed:0.0 green:1.0 blue:0.0 alpha:1.0];
    }
    return self;
}
```

3、 编写自定义的着色器代码。

```objective-c
NSString *const kQMFishEyeFilterFragmentShaderString = SHADER_STRING
(
 precision highp float;
 
 varying vec2 textureCoordinate;
 uniform sampler2D inputImageTexture;
 
 uniform float radius;
 
 const float PI = 3.1415926535;
 
 void main()
 {
     float aperture = 175.0;
     float apertureHalf = radius * aperture * (PI / 180.0);
     float maxFactor = sin(apertureHalf);
     
     vec2 uv;
     vec2 xy = 2.0 * textureCoordinate - 1.0;
     float d = length(xy);
     if (d < (2.0 - maxFactor)) {
         d = length(xy * maxFactor);
         float z = sqrt(1.0 - d * d);
         float r = atan(d, z) / PI;
         float phi = atan(xy.y, xy.x);
         
         uv.x = r * cos(phi) + radius;
         uv.y = r * sin(phi) + radius;
         
     }else {
         uv = textureCoordinate;
     }
     
     vec4 color = texture2D(inputImageTexture, uv);
     gl_FragColor = color;
 }
 );
```

4、 使用自定义滤镜特效。

```objective-c
#pragma mark - Events
- (IBAction)startButtonTapped:(UIButton *)sender
{
    // 加载图片
    GPUImagePicture *picture = [[GPUImagePicture alloc] initWithImage:[UIImage imageNamed:@"3.jpg"]];
    
    QMFishEyeFilter *filter = [[QMFishEyeFilter alloc] init];
    
    [picture addTarget:filter];
    [filter addTarget:_imageView];

    [picture processImage];
}
```
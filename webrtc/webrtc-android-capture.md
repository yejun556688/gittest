## [Video Capture](https://blog.piasy.com/2017/07/24/WebRTC-Android-Camera-Capture/)

WebRTC-Android 的camera采集主要涉及到以下几个类：`CameraEnumerator`，`CameraCapturer`，`CameraSession`，`SurfaceTextureHelper`。

其中 Enumerator 创建 Capturer，Capturer 创建 Session，实现对相机的操作，SurfaceTextureHelper 实现用 SurfaceTexture 接收数据。

#### CameraEnumerator

`CameraEnumerator` 接口如下:

```java
public interface CameraEnumerator {
  public String[] getDeviceNames();
  public boolean isFrontFacing(String deviceName);
  public boolean isBackFacing(String deviceName);
  public List<CaptureFormat> getSupportedFormats(String deviceName);
  public CameraVideoCapturer createCapturer(
      String deviceName, CameraVideoCapturer.CameraEventsHandler eventsHandler);
}
```

主要是获取设备列表、检查朝向、创建 Capturer。

#### CameraCapturer

WebRTC 视频采集的接口定义为 `VideoCapturer`，其中定义了初始化、启停、销毁等操作，以及接收启停事件、数据的回调。相机采集的实现是 `CameraCapturer`，针对不同的相机 API 又分为 `Camera1Capturer` 和 `Camera2Capturer`。相机采集大部分逻辑都封装在 `CameraCapturer` 中，只有创建 `CameraSession` 的代码在两个子类中有不同的实现。

下面分别看看 `VideoCapturer` 几个重要的 API 实现逻辑。

- `void initialize(SurfaceTextureHelper surfaceTextureHelper, Context applicationContext,CapturerObserver capturerObserver)`

- `void startCapture(int width, int height, int framerate)`

  `startCapture` 则会先检查当前是否正在创建 session，或者已有 session 正在运行，这里保证了不会同时存在多个 session 在运行。而众多状态成员的访问都通过 `stateLock` 进行保护，避免多线程安全问题。

  如果需要创建 session，则在相机操作线程创建 session，同时在主线程检测相机操作的超时。所有相机的操作都切换到了单独的相机线程，以避免造成主线程阻塞，而检查超时自然不能在相机线程，否则相机线程被阻塞住之后超时回调也不会执行。

  我们发现 capturer 中并没有实际相机操作的代码，开启相机、预览的代码都封装在了 `CameraSession` 中，那这样 capturer 的逻辑就得到了简化，切换摄像头、失败重试都只需要创建 session 即可，capturer 可以专注于状态维护和错误处理的逻辑。

  `CameraCapturer` 状态维护和错误处理的逻辑还是非常全面的：相机开启状态、相机运行状态、切换摄像头状态、错误重试、相机开启超时，全部都考虑到了。另外相机切换、开关相机、错误事件，统统都有回调通知。这里就充分体现出了 demo 和产品的差别，开启相机预览的 demo 十行代码就能搞定，而要全面考虑各种异常情况，就需要费一番苦心了。

  不过这里仍有一点小瑕疵，错误回调的参数是字符串，虽然可以很方便的打入日志，但不利于代码判断错误类型。最好是参数使用错误码，然后准备一个错误码到错误信息的转换函数。

- `stopCapture`

- `changeCaptureFormat`

- `switchCamera`

  #### CameraSession

  #### ​

  #### ​
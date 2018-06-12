#### 1 GLProgram

GLProgram专门处理OpenGL ES程序的创建等相关工作, 初始化OpenGL ES环境，编译、链接顶点着色器和片元着色器.

```c++
class GLProgram {
  public:
  	GLProgram();
  	~GLProgram();
  	bool InitShadersFromString(const std::string& vertexShaderSource,
                               const std::string& fragmentShaderSource);
 	bool Link();
  	void Use();
  	void Validate();
  	
    // Settings for this program
    GLuint GetAttribLocation(const std::string& attribute);
    GLuint GetUniformLocation(const std::string& uniformName);

    void SetUniformValue(const std::string& uniformName, int value);
    void SetUniformValue(const std::string& uniformName, float value);
    void SetUniformValue(const std::string& uniformName, Vector2 value);
    void SetUniformValue(const std::string& uniformName, Matrix3 value);
    void SetUniformValue(const std::string& uniformName, Matrix4 value);

    void SetUniformValue(int uniformLocation, int value);
    void SetUniformValue(int uniformLocation, float value);
    void SetUniformValue(int uniformLocation, Vector2 value);
    void SetUniformValue(int uniformLocation, Matrix3 value);
    void SetUniformValue(int uniformLocation, Matrix4 value);
  private:
  	// This two function called by InitShadersFromString()
  	bool InitVertexShaderSourceFromString(const std::string& vertexShaderSource);
  	bool InitFragmentShaderSourceFromString(const std::string& fragmentShaderSource);
  	initialized_;
  	GLunit program_;
  	GLuint vertShader_;
  	GLuint fragShader_;
};
```

#### 2 FramebufferInterface

处理帧缓存相关的类

```c++
class FramebufferInterface {
  public:
  	void UpdateFrame(I420VideoFrame* frame) = 0;
  	virtual ~FramebufferInterface();
};
```

> **2.1 CPUFramebuffer**

```c++
class CPUFramebuffer: public FramebufferInterface {
  public:
  	CPUFramebuffer();
  	void UpdateFrame(I420VideoFrame* frame);
  	~CPUFramebuffer();
};
```

> **2.2 GPUFramebuffer**

```c++
typedef struct GPUTextureOptions {
    GLenum minFilter;
    GLenum magFilter;
    GLenum wrapS;
    GLenum wrapT;
    GLenum internalFormat;
    GLenum format;
    GLenum type;
} GPUTextureOptions;

class GPUFramebuffer: public FramebufferInterface {
  public:
  	GPUFramebuffer(size_t width, size_t height);
  	void UpdateFrame(I420VideoFrame* frame);
  	~GPUFramebuffer();
  	void SetTextureOptions(GPUTextureOptions textureOptions);
  	GLuint GetTexture() { return texture_};
  private:
  	bool GenerateFramebuffer();
  	void DestroyFramebuffer();
  	size_t width_;
  	size_t height_;
  	GLuint texture_;
  	GPUTextureOptions textureOptions_;
};
```

#### 3 FrameInputInterface

这个类能接受帧缓存的输入，在响应链中每一个中间节点都能够接受输入经过它的处理之后又能输出给下一个节点。

```c++
class FrameInputInterface {
  public:
  virtual void SetInputFrameBuffer(FramebufferInterface* newInputFramebuffer) = 0;
};
```

#### 4 FrameOutputInterface

该类作为输出，输出的是`FrameBuffer`对象。实现一些最基本的方法，不依赖具体细节，细节处理在子类中完成。主要工作：1、`FrameBuffer`对象管理；2、响应链管理；3、图像提取。

```c++
class FrameOutputInterface {
  public:
  	// 1、framebuffer管理
  	// 设置Filter的帧缓存对象
    void SendFrameToNextFilter() {
		nextFilter_->SetInputFrameBuffer(outputFrameBuffer_);
    }
  
  	// 2、响应链管理
  	void SetNextFilter(FrameInputInterface* filter);

  	// 3、图像提取
	I420VideoFrame* FrameFromCurrentFramebuffer();
 
  private:
  	// 输出的帧缓存对象
  	FramebufferInterface* outputFrameBuffer_;
  	FrameInputInterface* nextFilter_;
};
```

#### 5 FilterInterface

```c++
enum FilterType {CPU_FILTER, GPU_FILTER};
class FilterInterface: public FrameInputInterface, public FrameOutputInterface {
  public:
  	FilterInterface();
  	virtual ~FilterInterface();
  	FilterType GetFilterType() { return filterType_;}
  	std::string GetFilterName() { return name_;}
  private:
  	FilterType filterType_;
  	std::string name_;
  	bool initialized_;
};
```

> **5.1 CPUFilter**

```c++
class CPUFilter: public FilterInterface {
  public:
  	CPUFilter(): filterType_(CPU_FILTER) {}
  	virtual ~CPUFilter();
  	// 实现FrameInputInterface
  	// 接收帧缓存对象
  	virtual void SetInputFrameBuffer(CPUFrameBuffer* newInputFrameBuffer);
  private:
  	void RenderToTexture();
};
```

> **5.2** **GPUFilter**

```c++
class GPUFilter: public FilterInterface {
  public:
  	GPUFilter(): filterType_(GPU_FILTER) {}
  	virtual ~GPUFilter();
  
  	// Initialize with vertex and fragment shaders
  	// Call GLProgram's InitShadersFromString() to init GLProgramObject
  	bool InitShadersFromString(const std::string& vertexShaderString,
                               const std::string& fragmentShaderString);
  	// Initialize with a fragment shader
  	bool InitWithFragmentShaderFromString(const std::string& fragmentShaderString);
  	// Initialize with a vertex shader
  	bool InitWithVertexShaderFromString(const std::string& vertexShaderString);
  	// 暴露GLProgram，外界便可以设置GLProgram
  	GLProgram* GetProgram() {return filterProgram;}
    	
  	// 实现FrameInputInterface
  	// 接收帧缓存对象
  	virtual void SetInputFrameBuffer(GPUFrameBuffer* newInputFrameBuffer);
    	
  private:
  	// 进行GL绘制
  	void RenderToTexture();
  
  	// 输入缓存对象
  	GPUFrameBuffer* firstInputFrameBuffer;
  	// GL程序
  	GLProgram* filterProgram;
  	// 顶点属性和纹理属性变量
  	GLint filterPositionAttribute;
    GLint filterTextureCoordinateAttribute;
  	// 纹理Uniform变量
    GLint filterInputTextureUniform;
};
```

#### 6 SourceFrame

链路的源头

```c++
class SourceFrame: public FrameOutputInterface
{
	public:
		static SourceFrame* Create();
        void UpdateFrame(I420VideoFrame* frame) {
        	 // 1. ConvertI420FrameToFramebuffer
            ConvertI420FrameToFramebuffer();
            // 2. setInputFramebuffer
            nextFilter_->SetInputFrameBuffer(outputFrameBuffer_);
        }
	private:
		SourceFrame();
		// 根据target filter类型 来选择Convert
		void ConvertI420FrameToFramebuffer();
		void ConvertI420FrameToCPUFramebuffer();
		void ConvertI420FrameToGPUFramebuffer();
};
```

#### 7 TargetFrame

链路的末端

```c++
class TargetFrame: pubilc FrameInputInterface
{
	public:
      virtual void SetInputFrameBuffer(FramebufferInterface* newInputFramebuffer);
      I420VideoFrame* GetI420VideoFrame();
      std::vector<I420VideoFrame*> GetI420VideoFrames();
   	private:
      I420VideoFrame* GetFrameFormCPUFramebuffer();
      I420VideoFrame* GetFrameFormGPUFramebuffer();
      std::vector<I420VideoFrame*> frame_list_;
};
```

#### 8 FilterPipeline

```c++
struct FilterInfo {
  std::string name;
  int start_time_ms;
  int stop_time_ms;
  int reserved;
};
class FilterPipeline {
  public:
 FilterPipeline(): filterCompleted_(false) { source_ = SourceFrame::Create(); }
  
  void AddFilter(FilterInterface* filter, bool lastOne = false) {
    if (filter->GetFilterType() == GPU_FILTER) {
      	gpuFilterList_.push_back(filter);
    } else {
      	cpuFilterList_.push_back(filter);
    }
    
    filtersNeedUpdate_ = lastOne;
  }
  
  void RemoveFilter(FilterInterface* filter);
  
  void UpdateFrame(I420VideoFrame* frame) {
    source_->UpdateFrame(frame);
  }
  
  I420VideoFrame* GetOutputFrame() {
    target_.FrameFormFramebuffer();
  }
  
  std::vector<I420VideoFrame*> GetOutputFrames() {
    target_->GetI420VideoFrames();
  }
  
  std::vector<FilterInfo*> GetFilterInfo();
  void SetFilterInfo(std::vector<FilterInfo*>);
 
  private:
    void UpdateFilters();
  	SourceFrame* source_;
  	TargetFrame target_;
  	std::vector<FilterInterface*> cpuFilterList_;
  	std::vector<FilterInterface*> gpuFilterList_;
  	std::vector<FilterInfo*> filtersInfo_;
  	bool filtersNeedUpdate_;
};
```

#### 9 示例

```c++
FilterPipeline filterPipline;
GPUFilter filter1, filter2;
filterPipline.AddFilter(&filter1);
filterPipline.AddFilter(&filter2, true);
while(true) {
  filterPipline.UpdateFrame(frame);
  outFrame = filterPipline.GetOutputFrame();
}
```

```
compile 'jp.co.cyberagent.android.gpuimage:gpuiamge-library:1.4.1'
```


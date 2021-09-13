# Shader 开发

|原文     | [Shader Develoment](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/Rendering/ShaderDevelopment/)|
|:---     |:---|
|作者     | Unreal Engine 4.27 Documentation|
|翻译     | HumanGao|
|校对     | 暂未校对|

## Shaders 和 Materials
### **全局 Shaders**
  全局着色器是在混合几何（像一个全屏的四边形？quad）上，不需要材质接口就可以操作的着色器。例子将是阴影过滤或者后处理。在内存中，对于任何全局着色器类型仅仅存在一个着色器。
### **材质和网格类型** 
  材质是由一组控制材质渲染方式的状态定义的(包括混合模式，双面等)和一组控制材质如何与各种渲染pass(BaseColor, Roughness, Normal等)交互的材质输入来定义的。
### **顶点工厂** 
  材质必须支持被应用到不同的网格类型，这个特性被顶点工厂完成。FVertexFactoryType 代表了唯一的网格类型，FVertexFactory 
实例存储着每一个实例支持唯一网格类型的数据。例如，FGPUSkinVertexFactory 存储剥皮的骨骼矩阵，以及关于 GPU skin 顶点工厂着色器代码作为输入所需要的 各种各样的顶点缓冲。顶点工厂着色器代码是一个隐式的接口，它被不同的pass着色器使用去抽象网格类型差异。顶点工厂主要包括顶点着色器代码，以及一些像素（片段）着色器代码。一些重要的顶点工厂着色器代码的组件如下表所示。

|函数     |描述     |
|:---     |:---     |
|FVertexFactoryInput     | |
|FVertexFactoryIntermediates    ||
|FVertexFactoryInterpolantsVSToPS     | |
|VertexFactoryGetWorldPosition     | |
|VertexFactoryGetInterpolantsVSToPS     | |
|GetMaterialPixelParameters     | |

### **材质Shaders**

### **创建一个材质Shaders**

### **特殊的引擎材质**

## 着色器编译

## 着色器缓存和烘培

## 添加全局着色器到 Unreal Engine
**全局着色器**是不需要材质编辑器创建的着色器。相反，他们是用C++创建的，在固定几何体上操作并且不需要材质或者网格接口。有时需要更高级的功能去是实现一个预期效果并且为了达到效果，一个自定义的着色器pass也是必要的。
一些全局着色器的例子将会被后加工效果渲染，调度计算机着色器和清屏。
### 虚幻着色器文件
Unreal Engine使用Unreal Shader file (.usf) 去存储和读取它使用的着色器的信息。任何新的shader的源文件创建需要被存储到Engine/Shaders文件夹。如果shader是插件的一部分，他需要被存储到 Plugin/Shaders。
>**Tip**
>
> - 在你的**ConsoleVariables.ini**文件中 使用命令 **r.ShaderDevelopmentMode=1**获取着色器编译过程中的细节日志。
> 
> - 看**Shader 开发**部分获取更多的信息
### 全局着色器例子
作为一个例子，我们将创建一个简单的传递（Pass-Through）顶点着色器和一个像素（片段）着色器，并且返回一个自定义的颜色。
#### 全局着色器例子

通过创建一个新的文本文件在你的 Engine/Shaders文件夹。用 **.usf** 重命名他的文件扩展名。下面的例子使用 **MyTest.usf**。

接下来，添加下面的代码到 **MyTest.usf** 文件：
> **MyTest.usf**
```txt
// Simple pass-through vertex shader

void MainVS(
    in float4 InPosition : ATTRIBUTE0,
    out float4 Output : SV_POSITION
)
{
    Output = InPosition;
}

// Simple solid color pixel shader
float4 MyColor;
float4 MainPS() : SV_Target0
{
    return MyColor;
}
```
#### 类声明
> **MyTestVS.h**
#include "GlobalShader.h"
```c++
// This can go on a header or cpp file
class FMyTestVS : public FGlobalShader
{
    DECLARE_EXPORTED_SHADER_TYPE(FMyTestVS, Global, /*MYMODULE_API*/);

    FMyTestVS() { }
    FMyTestVS(const ShaderMetaType::CompiledShaderInitializerType& Initializer)
        : FGlobalShader(Initializer)
    {
    }

    static bool ShouldCache(EShaderPlatform Platform)
    {
        return true;
    }
};
```
做这个的时候有几个需求
- 这是FGlobalShader的子类. 就其本身而论,它将在全局着色器映射中结束, 意味着它不需要一个材质去找到它.

- DECLARE_EXPORTED_SHADER_TYPE() 宏的使用生成了序列化着色器类型所需要的主配置文件.如果需要的话，第三个参数是一个对于着色器模块将会存储的代码模块外部链接的类型. 例如, 任何C++代码都不在Renderer模块中.

- 有两个构造器: 默认构造器,序列化构造器.

- 在确定的环境下，ShouldCache()函数被用作决定这个着色器是否被编译. 例如, 你可能不想在一个非计算着色器的RHI上编译一个计算着色器.
#### 注册一个着色器类型
#### 声明一个像素（片段）着色器
#### 写完一个简单的函数


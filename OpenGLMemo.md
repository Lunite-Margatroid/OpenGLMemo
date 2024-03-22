## 顶点缓冲 VertexBuffer

```c++
unsigned int vbo;
glGenBuffers(1, &vbo);				// 创建缓冲对象  可以创建复数个
glCreateBuffers(1, &vbo);			// 与glGenBuffers作用一样
glBindBuffer(GL_ARRAY_BUFFER, vbo);	// 绑定
// 参数1可以是table1之一
glBufferData(GL_ARRAY_BUFFER, size, data, GL_STATIC_DRAW);
// 分配空间
// 如果data不为NULL 同时将data指向的数据写入申请的空间
// 参数4可以是table2之一

glBufferStorage(GL_ARRAY_BUFFER, size, data, GL_STATIC_DRAW);	// 与glBufferData作用相同

glDeleteBuffer(1, &vbo); // 释放buffer
```

## 索引缓冲 ElementBuffer

```c++
unsigned int ebo;
glGenBuffers(1, &ebo);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, ebo);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, count * sizeof(unsigned int), data, GL_STATIC_DRAW);
glDeleteBuffer(1, &ebo);
```

## 顶点数组 VertexArray

```c++
unsigned int vao;
glGenVertexArrays(1, &vao);

// 先绑定顶点缓冲和索引缓冲
glBindBuffer(GL_ARRAY_BUFFER, vbo);
glBindBuffer(GL_ELEMENT_BUFFER, ebo);
// 设置顶点属性
glVertexAttribPointer();
for()
{
	glVertexAttribPointer(i, attr.count , attr.type, attr.bNormalize,m_stride, (void*)offset);
	glEnableVertexAttribArray(i);
}
glDeleteVertexArrays(1, &vao);
```

## 着色器 Shader

### 着色器的初始化

```c++
// 创建着色器程序对象
unsigned int shaderProgram = glCreateProgram();

// 创建着色器对象
unsigned int verShader;
verShader = glCreateShader(GL_VERTEX_SAHDER);
// 加载着色器代码
std::string strShaderSource;
GetShaderSource(path, strShaderSource);
const char* strSource = strShaderSource.c_str();
// 将代码绑定到着色器
glShaderSource(verShader, 1, &strSource, NULL);
// 编译着色器
glCompileShader(verShader);
// 编译查错
int success;
char infoLog[512];
glGetShaderiv(nShaderID, GL_COMPILE_STATUS, &success);
if (!success)
{
	glGetShaderInfoLog(verShader, 512, NULL, infoLog);
	std::cout << strType << " compile error!\n" << infoLog << std::endl;
}

// 将着色器对象附加到着色器程序对象
glAttachShader(shaderProgram, verShader);

// 链接
glLinkProgram(shaderProgram);

// 启用着色器程序
glUseProgram(shaderProgram);

// 删除着色器程序
glDeleteProgram(shaderProgram);
```

### 从文件读取着色器代码的函数

```c++
	void Shader::GetShaderSource(const std::string& path, std::string& shaderSource)
	{
		std::ifstream infile;
		std::stringstream sstr;
		infile.open(path, std::ios::in);
		if (infile)
		{
			while (!infile.eof())
			{
				std::string str_t;
				getline(infile, str_t);
				sstr << str_t << '\n';
			}
		}
		else
		{
			std::cout << "Can't open file" << "\" " << path << "\" !" << std::endl;
			infile.close();
			__debugbreak();
			return;
		}

		shaderSource = sstr.str();
		infile.close();
		return;
	}
```

### Uniform变量的赋值

```c++
// 获得变量位置
int location = glGetUniformLocation(shaderProgram, valueName);
if(location == -1)	// 没有找到变量名
{
    std::cout << "Can't find uniform value '" << valueName << "' !" << std::endl;
}

// 设置变量value
glUniformXxxxxxx(location, ... );

// 优化
std::unordered_map<std::string, int> m_UniformMapLocation; // 使用散列表记录变量位置
int location = GetUniformLocation(valueName);
int Shader::GetUniformLocation(const std::string& valueName)
	{
		if (m_UniformMapLocation.find(valueName) != m_UniformMapLocation.end())
			return m_UniformMapLocation[valueName];

		GLCall(int location = glGetUniformLocation(m_ShaderID, valueName.c_str()));
		if (location == -1)
		{
			std::cout << "Can't find uniform value '" << valueName << "' !" << std::endl;
		}
		m_UniformMapLocation[valueName] = location;
		return location;
	}
```

## 2D纹理 Texture

```c++
unsigned int texture;
glGenTextures(1, &texture);				// 生成纹理对象
glActiveTexture(GL_TEXTURE0 + index);	// 激活纹理单元0~79
glBindTexture(GL_TEXTURE_2D, texture);	// 将纹理对象绑定到激活的纹理单元
glTexImage2D(GL_TEXTURE_2D, 0, texColorMode, m_nWidth, m_nHeight, 0, resColorMode, GL_UNSIGNED_BYTE, img_data);								// 开辟空间并写入纹理数据

// 设置纹理属性
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);		// 设置环绕方式

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);	// 设置插值方式

glGenerateMipmap(GL_TEXTURE_2D);	// 自动生成多级渐远级别纹理(mipmap)
```

### 环绕方式和插值方式

一个纹理对象的环绕方式和插值方式必须要由用户定义。

**环绕方式**`GL_TEXTURE_WRAP_S` `GL_TEXTURE_WRAP_T`.可以是`GL_CLAMP_TO_EDGE`, `GL_CLAMP_TO_BORDER`, `GL_MIRRORED_REPEAT`, `GL_REPEAT`, or `GL_MIRROR_CLAMP_TO_EDGE`

对于立方体纹理，还有第三维的环绕方式`GL_TEXTURE_WRAP_R`.可以是 `GL_CLAMP_TO_EDGE`, `GL_CLAMP_TO_BORDER`, `GL_MIRRORED_REPEAT`, `GL_REPEAT`, or `GL_MIRROR_CLAMP_TO_EDGE`

设置border颜色

```c++
float color[4] = {0.3f, 0.3f, 0.4f ,1.0f};
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, color);
```

**插值方式**

`GL_TEXTURE_MIN_FILTER`,`GL_TEXTURE_MAG_FILTER`

对于`GL_TEXTURE_MAG_FILTER` 只能是

`GL_NEAREST`,从最邻近的像素取值

`GL_LINEAR`,线性插值

对于`GL_TEXTURE_MIN_FILTER`

`GL_NEAREST`邻近像素

`GL_LINEAR`线性插值

`GL_NEAREST_MIPMAP_NEAREST`选择最邻近的mipmap然后取临近像素

`GL_LINEAR_MIPMAP_NEAREST`选择最邻近的mipmap然后线性插值

`GL_NEAREST_MIPMAP_LINEAR`选择最邻近的两个mipmap分别线性插值，得到两个值，然后取这两个值的平均

`GL_LINEAR_MIPMAP_LINEAR`选择最邻近的两个mipmap分别线性插值，得到两个值，然后取这两个值的加权平均（线性插值）

# 函数

## glBufferData

| `void **glBufferData**(` | GLenum target,     |
| ------------------------ | ------------------ |
|                          | GLsizeiptr size,   |
|                          | const void * data, |
|                          | GLenum usage`)`;   |

**target:**

缓冲对象类型，可以是table1的枚举

**size:**

申请空间的大小，单位字节。

**data:**

如果不为NULL，将data指向的数据size字节复制到缓冲中

**usage:**

`GL_STREAM_DRAW`, `GL_STREAM_READ`, `GL_STREAM_COPY`, `GL_STATIC_DRAW`, `GL_STATIC_READ`, `GL_STATIC_COPY`, `GL_DYNAMIC_DRAW`, `GL_DYNAMIC_READ`, or `GL_DYNAMIC_COPY`.

**枚举中单词对应含义：**

STREAM

只初始化一次，缓冲数据不频繁使用

STATIC

只初始化一次，频繁使用

DYNAMIC

可多次修改，频繁使用

DRAW

缓冲被应用程序修改，数据用来渲染

READ

缓冲数据从GL读取，应用程序可以访问

COPY

缓冲数据从GL读取，数据用来渲染





## glVertexAttribPointer

| `void **glVertexAttribPointer**(` | GLuint index,            |
| --------------------------------- | ------------------------ |
|                                   | GLint size,              |
|                                   | GLenum type,             |
|                                   | GLboolean normalized,    |
|                                   | GLsizei stride,          |
|                                   | const void * pointer`)`; |

**index:**

顶点属性的索引，从0开始。

**size:**

当前属性的维度。

**type:**

当前属性每一个维度的数据类型。

**normalized:**

是否对数据进行归一化处理。

对于整数类型，且该项参数为GL_FALSE, 传入着色器之后会强转为浮点型。如4->4.0f.

对于整数类型，且该项参数为GL_TRUE,传入着色器之后会以最大值为基准归一化为[0.0f,1.0f]的浮点数。

如果要使用整数型属性，可以使用glVertexAttribIPointer()

**stride:**

前一个顶点属性与后一个顶点属性的步幅。

**pointer:**

第一个顶点的对应属性与array buffer起始位置的偏移量，单位为字节。

## glEnableVertexAttribArray

| `void **glEnableVertexAttribArray**(` | GLuint index`)`; |
| ------------------------------------- | ---------------- |
|                                       |                  |

| `void **glDisableVertexAttribArray**(` | GLuint index`)`; |
| -------------------------------------- | ---------------- |
|                                        |                  |

使索引为index的属性生效/失效。

glEableVertexArrayAttrib是它的Named版本

## glDrawArrays

`void glDrawArrays(GLenum mode, GLint first, GLsizei count)`;

**mode:**

图元类型

`GL_POINTS`, `GL_LINE_STRIP`, `GL_LINE_LOOP`, `GL_LINES`, `GL_LINE_STRIP_ADJACENCY`, `GL_LINES_ADJACENCY`, `GL_TRIANGLE_STRIP`, `GL_TRIANGLE_FAN`, `GL_TRIANGLES`, `GL_TRIANGLE_STRIP_ADJACENCY`, `GL_TRIANGLES_ADJACENCY` and `GL_PATCHES`

**first:**

第一个顶点的索引

**count:**

顶点的数量

## glDrawElements

| `void glDrawElements(` | GLenum mode,             |
| ---------------------- | ------------------------ |
|                        | GLsizei count,           |
|                        | GLenum type,             |
|                        | const void * indices`)`; |

**mode:**

图元类型

**count:**

索引的数量

**type:**

索引的数据类型

**indices:**

读取索引的偏移量。相对于element buffer的头地址。单位为字节。

## glCreateShader

`GLuint glCreateShader(GLenum shaderType)`; 

**shaderType:**

着色器类型

`GL_COMPUTE_SHADER`, `GL_VERTEX_SHADER`, `GL_TESS_CONTROL_SHADER`, `GL_TESS_EVALUATION_SHADER`, `GL_GEOMETRY_SHADER`, or `GL_FRAGMENT_SHADER`.

## glShaderSource

| `void **glShaderSource**(` | GLuint shader,          |
| -------------------------- | ----------------------- |
|                            | GLsizei count,          |
|                            | const GLchar **string,  |
|                            | const GLint *length`)`; |

**shader:**

着色器的句柄

**count:**

参数3和参数4数组的长度

**string:**

字符串数组

**length:**

整数数组，标识字符串数组中每一个字符串的长度，结尾的'\0'不计入。

如果该参数为NULL，则认为字符串都是以空字符结尾的字符串。

## glTexImage2D

| `void **glTexImage2D**(` | GLenum target,        |
| ------------------------ | --------------------- |
|                          | GLint level,          |
|                          | GLint internalformat, |
|                          | GLsizei width,        |
|                          | GLsizei height,       |
|                          | GLint border,         |
|                          | GLenum format,        |
|                          | GLenum type,          |
|                          | const void * data`)`; |

**`target`**

纹理对象类型 `GL_TEXTURE_2D`, `GL_PROXY_TEXTURE_2D`, `GL_TEXTURE_1D_ARRAY`, `GL_PROXY_TEXTURE_1D_ARRAY`, `GL_TEXTURE_RECTANGLE`, `GL_PROXY_TEXTURE_RECTANGLE`, `GL_TEXTURE_CUBE_MAP_POSITIVE_X`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_X`, `GL_TEXTURE_CUBE_MAP_POSITIVE_Y`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_Y`, `GL_TEXTURE_CUBE_MAP_POSITIVE_Z`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_Z`, or `GL_PROXY_TEXTURE_CUBE_MAP`.

**`level`**

逐级渐远纹理（mipmap）的等级。0为基等级。

 If *`target`* is `GL_TEXTURE_RECTANGLE` or `GL_PROXY_TEXTURE_RECTANGLE`, *`level`* must be 0.

**`internalformat`**

纹理对象的内部格式。常用的是`GL_RGB`,`GL_RGBA`

深度贴图用`GL_DEPTH_COMPONENT` 

**`width`**

纹理的高度. 所有的实现至少都支持1024

**`height`**

纹理的宽度，至少支持1024. 如果是1维纹理数组，则为纹理的索引至少支持256. `GL_TEXTURE_1D_ARRAY` and `GL_PROXY_TEXTURE_1D_ARRAY` targets. 

**`border`**

必须为0

**`format`**

从data读取的颜色格式。

Specifies the format of the pixel data. The following symbolic values are accepted: `GL_RED`, `GL_RG`, `GL_RGB`, `GL_BGR`, `GL_RGBA`, `GL_BGRA`, `GL_RED_INTEGER`, `GL_RG_INTEGER`, `GL_RGB_INTEGER`, `GL_BGR_INTEGER`, `GL_RGBA_INTEGER`, `GL_BGRA_INTEGER`, `GL_STENCIL_INDEX`, `GL_DEPTH_COMPONENT`, `GL_DEPTH_STENCIL`.

**`type`**

从data读取的数据类型。

Specifies the data type of the pixel data. The following symbolic values are accepted: `GL_UNSIGNED_BYTE`, `GL_BYTE`, `GL_UNSIGNED_SHORT`, `GL_SHORT`, `GL_UNSIGNED_INT`, `GL_INT`, `GL_HALF_FLOAT`, `GL_FLOAT`, `GL_UNSIGNED_BYTE_3_3_2`, `GL_UNSIGNED_BYTE_2_3_3_REV`, `GL_UNSIGNED_SHORT_5_6_5`, `GL_UNSIGNED_SHORT_5_6_5_REV`, `GL_UNSIGNED_SHORT_4_4_4_4`, `GL_UNSIGNED_SHORT_4_4_4_4_REV`, `GL_UNSIGNED_SHORT_5_5_5_1`, `GL_UNSIGNED_SHORT_1_5_5_5_REV`, `GL_UNSIGNED_INT_8_8_8_8`, `GL_UNSIGNED_INT_8_8_8_8_REV`, `GL_UNSIGNED_INT_10_10_10_2`, and `GL_UNSIGNED_INT_2_10_10_10_REV`.

**`data`**

Specifies a pointer to the image data in memory.

如果为NULL则不读取数据。对于部分类型的纹理对象也不读取数据。

# Table

## table1

| **Buffer Binding Target**      | **Purpose**                        |
| :----------------------------- | :--------------------------------- |
| `GL_ARRAY_BUFFER`              | Vertex attributes                  |
| `GL_ATOMIC_COUNTER_BUFFER`     | Atomic counter storage             |
| `GL_COPY_READ_BUFFER`          | Buffer copy source                 |
| `GL_COPY_WRITE_BUFFER`         | Buffer copy destination            |
| `GL_DISPATCH_INDIRECT_BUFFER`  | Indirect compute dispatch commands |
| `GL_DRAW_INDIRECT_BUFFER`      | Indirect command arguments         |
| `GL_ELEMENT_ARRAY_BUFFER`      | Vertex array indices               |
| `GL_PIXEL_PACK_BUFFER`         | Pixel read target                  |
| `GL_PIXEL_UNPACK_BUFFER`       | Texture data source                |
| `GL_QUERY_BUFFER`              | Query result buffer                |
| `GL_SHADER_STORAGE_BUFFER`     | Read-write storage for shaders     |
| `GL_TEXTURE_BUFFER`            | Texture data buffer                |
| `GL_TRANSFORM_FEEDBACK_BUFFER` | Transform feedback buffer          |
| `GL_UNIFORM_BUFFER`            | Uniform block storage              |

## table2

`GL_STREAM_DRAW`, `GL_STREAM_READ`, `GL_STREAM_COPY`, `GL_STATIC_DRAW`, `GL_STATIC_READ`, `GL_STATIC_COPY`, `GL_DYNAMIC_DRAW`, `GL_DYNAMIC_READ`, or `GL_DYNAMIC_COPY`.

# ImGui

## 窗口控件

### 文本

```c++
ImGui::Text("This is some useful text.");
```

### 格式化文本

```c++
ImGui::Text("counter = %d", counter);
```

### CheckBox 单选框

```c++
ImGui::Checkbox("Demo Window", &show_demo_window);
```

### 单个float数值滑块

```c++
ImGui::SliderFloat("float", &f, 0.0f, 1.0f);
```

### RGB拾色器

```c++
ImGui::ColorEdit3("clear color", (float*)&clear_color);
```

### Button 按钮

```c++
// Buttons return true when clicked (most widgets return true when edited/activated)
if (ImGui::Button("Button"))                            
	counter++;
```

### 同一行 / 不换行

```c++
ImGui::SameLine();
```


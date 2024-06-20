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

## 加载字体

```c++
ImGuiIO& io = ImGui::GetIO();(void)io;
io.Fonts->AddFontFromFileTTF(filepath, fontSize, NULL, io.Fonts->GetGlyphRangesChineseFull());
```

## 设置风格

方法大体是在初始化的时候使用`ImGui::GetStyle()`获取style结构体，然后修改成员变量。

### 颜色风格

直接看`StyleColorsDark`, `StyleColorsLight`, `StyleColorsClassic`的函数体就一目了然了。

```c++
void ImGui::StyleColorsLight(ImGuiStyle* dst)
{
    ImGuiStyle* style = dst ? dst : &ImGui::GetStyle();
    ImVec4* colors = style->Colors;

    colors[ImGuiCol_Text]                   = ImVec4(0.00f, 0.00f, 0.00f, 1.00f);
    colors[ImGuiCol_TextDisabled]           = ImVec4(0.60f, 0.60f, 0.60f, 1.00f);
    colors[ImGuiCol_WindowBg]               = ImVec4(0.94f, 0.94f, 0.94f, 1.00f);
    colors[ImGuiCol_ChildBg]                = ImVec4(0.00f, 0.00f, 0.00f, 0.00f);
    colors[ImGuiCol_PopupBg]                = ImVec4(1.00f, 1.00f, 1.00f, 0.98f);
    colors[ImGuiCol_Border]                 = ImVec4(0.00f, 0.00f, 0.00f, 0.30f);
    colors[ImGuiCol_BorderShadow]           = ImVec4(0.00f, 0.00f, 0.00f, 0.00f);
    colors[ImGuiCol_FrameBg]                = ImVec4(1.00f, 1.00f, 1.00f, 1.00f);
    colors[ImGuiCol_FrameBgHovered]         = ImVec4(0.26f, 0.59f, 0.98f, 0.40f);
    colors[ImGuiCol_FrameBgActive]          = ImVec4(0.26f, 0.59f, 0.98f, 0.67f);
    colors[ImGuiCol_TitleBg]                = ImVec4(0.96f, 0.96f, 0.96f, 1.00f);
    colors[ImGuiCol_TitleBgActive]          = ImVec4(0.82f, 0.82f, 0.82f, 1.00f);
    colors[ImGuiCol_TitleBgCollapsed]       = ImVec4(1.00f, 1.00f, 1.00f, 0.51f);
    colors[ImGuiCol_MenuBarBg]              = ImVec4(0.86f, 0.86f, 0.86f, 1.00f);
    colors[ImGuiCol_ScrollbarBg]            = ImVec4(0.98f, 0.98f, 0.98f, 0.53f);
    colors[ImGuiCol_ScrollbarGrab]          = ImVec4(0.69f, 0.69f, 0.69f, 0.80f);
    colors[ImGuiCol_ScrollbarGrabHovered]   = ImVec4(0.49f, 0.49f, 0.49f, 0.80f);
    colors[ImGuiCol_ScrollbarGrabActive]    = ImVec4(0.49f, 0.49f, 0.49f, 1.00f);
    colors[ImGuiCol_CheckMark]              = ImVec4(0.26f, 0.59f, 0.98f, 1.00f);
    colors[ImGuiCol_SliderGrab]             = ImVec4(0.26f, 0.59f, 0.98f, 0.78f);
    colors[ImGuiCol_SliderGrabActive]       = ImVec4(0.46f, 0.54f, 0.80f, 0.60f);
    colors[ImGuiCol_Button]                 = ImVec4(0.26f, 0.59f, 0.98f, 0.40f);
    colors[ImGuiCol_ButtonHovered]          = ImVec4(0.26f, 0.59f, 0.98f, 1.00f);
    colors[ImGuiCol_ButtonActive]           = ImVec4(0.06f, 0.53f, 0.98f, 1.00f);
    colors[ImGuiCol_Header]                 = ImVec4(0.26f, 0.59f, 0.98f, 0.31f);
    colors[ImGuiCol_HeaderHovered]          = ImVec4(0.26f, 0.59f, 0.98f, 0.80f);
    colors[ImGuiCol_HeaderActive]           = ImVec4(0.26f, 0.59f, 0.98f, 1.00f);
    colors[ImGuiCol_Separator]              = ImVec4(0.39f, 0.39f, 0.39f, 0.62f);
    colors[ImGuiCol_SeparatorHovered]       = ImVec4(0.14f, 0.44f, 0.80f, 0.78f);
    colors[ImGuiCol_SeparatorActive]        = ImVec4(0.14f, 0.44f, 0.80f, 1.00f);
    colors[ImGuiCol_ResizeGrip]             = ImVec4(0.35f, 0.35f, 0.35f, 0.17f);
    colors[ImGuiCol_ResizeGripHovered]      = ImVec4(0.26f, 0.59f, 0.98f, 0.67f);
    colors[ImGuiCol_ResizeGripActive]       = ImVec4(0.26f, 0.59f, 0.98f, 0.95f);
    colors[ImGuiCol_Tab]                    = ImLerp(colors[ImGuiCol_Header],       colors[ImGuiCol_TitleBgActive], 0.90f);
    colors[ImGuiCol_TabHovered]             = colors[ImGuiCol_HeaderHovered];
    colors[ImGuiCol_TabActive]              = ImLerp(colors[ImGuiCol_HeaderActive], colors[ImGuiCol_TitleBgActive], 0.60f);
    colors[ImGuiCol_TabUnfocused]           = ImLerp(colors[ImGuiCol_Tab],          colors[ImGuiCol_TitleBg], 0.80f);
    colors[ImGuiCol_TabUnfocusedActive]     = ImLerp(colors[ImGuiCol_TabActive],    colors[ImGuiCol_TitleBg], 0.40f);
    colors[ImGuiCol_DockingPreview]         = colors[ImGuiCol_Header] * ImVec4(1.0f, 1.0f, 1.0f, 0.7f);
    colors[ImGuiCol_DockingEmptyBg]         = ImVec4(0.20f, 0.20f, 0.20f, 1.00f);
    colors[ImGuiCol_PlotLines]              = ImVec4(0.39f, 0.39f, 0.39f, 1.00f);
    colors[ImGuiCol_PlotLinesHovered]       = ImVec4(1.00f, 0.43f, 0.35f, 1.00f);
    colors[ImGuiCol_PlotHistogram]          = ImVec4(0.90f, 0.70f, 0.00f, 1.00f);
    colors[ImGuiCol_PlotHistogramHovered]   = ImVec4(1.00f, 0.45f, 0.00f, 1.00f);
    colors[ImGuiCol_TableHeaderBg]          = ImVec4(0.78f, 0.87f, 0.98f, 1.00f);
    colors[ImGuiCol_TableBorderStrong]      = ImVec4(0.57f, 0.57f, 0.64f, 1.00f);   // Prefer using Alpha=1.0 here
    colors[ImGuiCol_TableBorderLight]       = ImVec4(0.68f, 0.68f, 0.74f, 1.00f);   // Prefer using Alpha=1.0 here
    colors[ImGuiCol_TableRowBg]             = ImVec4(0.00f, 0.00f, 0.00f, 0.00f);
    colors[ImGuiCol_TableRowBgAlt]          = ImVec4(0.30f, 0.30f, 0.30f, 0.09f);
    colors[ImGuiCol_TextSelectedBg]         = ImVec4(0.26f, 0.59f, 0.98f, 0.35f);
    colors[ImGuiCol_DragDropTarget]         = ImVec4(0.26f, 0.59f, 0.98f, 0.95f);
    colors[ImGuiCol_NavHighlight]           = colors[ImGuiCol_HeaderHovered];
    colors[ImGuiCol_NavWindowingHighlight]  = ImVec4(0.70f, 0.70f, 0.70f, 0.70f);
    colors[ImGuiCol_NavWindowingDimBg]      = ImVec4(0.20f, 0.20f, 0.20f, 0.20f);
    colors[ImGuiCol_ModalWindowDimBg]       = ImVec4(0.20f, 0.20f, 0.20f, 0.35f);
}
```

### 其他风格

直接看style结构体的定义

```c++
struct ImGuiStyle
{
    float       Alpha;                      
    // Global alpha applies to everything in Dear ImGui.
    // 应用于全局的alpha值
    float       DisabledAlpha;             
    // Additional alpha multiplier applied by BeginDisabled(). Multiply over current value of Alpha.
    // 控件进入禁用状态时所乘的alpha值
    ImVec2      WindowPadding;              
    // Padding within a window.
    // 窗口边界预留空白 单位大概是像素
    float       WindowRounding;
    // Radius of window corners rounding. Set to 0.0f to have rectangular windows. Large values tend to lead to variety of artifacts and are not recommended.
    float       WindowBorderSize;
    // Thickness of border around windows. Generally set to 0.0f or 1.0f. (Other values are not well tested and more CPU/GPU costly).
    ImVec2      WindowMinSize;              
    // Minimum window size. This is a global setting. If you want to constrain individual windows, use SetNextWin dowSizeConstraints().
    ImVec2      WindowTitleAlign;           
    // Alignment for title bar text. Defaults to (0.0f,0.5f) for left-aligned,vertically centered.
    ImGuiDir    WindowMenuButtonPosition;   
    // Side of the collapsing/docking button in the title bar (None/Left/Right). Defaults to ImGuiDir_Left.
    float       ChildRounding;              
    // Radius of child window corners rounding. Set to 0.0f to have rectangular windows.
    float       ChildBorderSize;            
    // Thickness of border around child windows. Generally set to 0.0f or 1.0f. (Other values are not well tested and more CPU/GPU costly).
    float       PopupRounding;              
    // Radius of popup window corners rounding. (Note that tooltip windows use WindowRounding)
    float       PopupBorderSize;            
    // Thickness of border around popup/tooltip windows. Generally set to 0.0f or 1.0f. (Other values are not well tested and more CPU/GPU costly).
    ImVec2      FramePadding;               
    // Padding within a framed rectangle (used by most widgets).
    float       FrameRounding;              
    // Radius of frame corners rounding. Set to 0.0f to have rectangular frame (used by most widgets).
    float       FrameBorderSize;            
    // Thickness of border around frames. Generally set to 0.0f or 1.0f. (Other values are not well tested and more CPU/GPU costly).
    ImVec2      ItemSpacing;                
    // Horizontal and vertical spacing between widgets/lines.
    ImVec2      ItemInnerSpacing;           
    // Horizontal and vertical spacing between within elements of a composed widget (e.g. a slider and its label).
    ImVec2      CellPadding;                
    // Padding within a table cell. CellPadding.y may be altered between different rows.
    ImVec2      TouchExtraPadding;          
    // Expand reactive bounding box for touch-based system where touch position is not accurate enough. Unfortunately we don't sort widgets so priority on overlap will always be given to the first widget. So don't grow this too much!
    float       IndentSpacing;              
    // Horizontal indentation when e.g. entering a tree node. Generally == (FontSize + FramePadding.x*2).
    float       ColumnsMinSpacing;          
    // Minimum horizontal spacing between two columns. Preferably > (FramePadding.x + 1).
    float       ScrollbarSize;              
    // Width of the vertical scrollbar, Height of the horizontal scrollbar.
    float       ScrollbarRounding;          
    // Radius of grab corners for scrollbar.
    float       GrabMinSize;                
    // Minimum width/height of a grab box for slider/scrollbar.
    float       GrabRounding;               
    // Radius of grabs corners rounding. Set to 0.0f to have rectangular slider grabs.
    float       LogSliderDeadzone;          
    // The size in pixels of the dead-zone around zero on logarithmic sliders that cross zero.
    float       TabRounding;                
    // Radius of upper corners of a tab. Set to 0.0f to have rectangular tabs.
    float       TabBorderSize;              
    // Thickness of border around tabs.
    float       TabMinWidthForCloseButton;  
    // Minimum width for close button to appear on an unselected tab when hovered. Set to 0.0f to always show when hovering, set to FLT_MAX to never show close button unless selected.
    float       TabBarBorderSize;           
    // Thickness of tab-bar separator, which takes on the tab active color to denote focus.
    float       TableAngledHeadersAngle;    
    // Angle of angled headers (supported values range from -50.0f degrees to +50.0f degrees).
    ImGuiDir    ColorButtonPosition;        
    // Side of the color button in the ColorEdit4 widget (left/right). Defaults to ImGuiDir_Right.
    ImVec2      ButtonTextAlign;            
    // Alignment of button text when button is larger than text. Defaults to (0.5f, 0.5f) (centered).
    ImVec2      SelectableTextAlign;        
    // Alignment of selectable text. Defaults to (0.0f, 0.0f) (top-left aligned). It's generally important to keep this left-aligned if you want to lay multiple items on a same line.
    float       SeparatorTextBorderSize;    
    // Thickkness of border in SeparatorText()
    ImVec2      SeparatorTextAlign;         
    // Alignment of text within the separator. Defaults to (0.0f, 0.5f) (left aligned, center).
    ImVec2      SeparatorTextPadding;       
    // Horizontal offset of text from each edge of the separator + spacing on other axis. Generally small values. .y is recommended to be == FramePadding.y.
    ImVec2      DisplayWindowPadding;       
    // Window position are clamped to be visible within the display area or monitors by at least this amount. Only applies to regular windows.
    ImVec2      DisplaySafeAreaPadding;     
    // If you cannot see the edges of your screen (e.g. on a TV) increase the safe area padding. Apply to popups/tooltips as well regular windows. NB: Prefer configuring your TV sets correctly!
    float       DockingSeparatorSize;       
    // Thickness of resizing border between docked windows
    float       MouseCursorScale;           
    // Scale software rendered mouse cursor (when io.MouseDrawCursor is enabled). We apply per-monitor DPI scaling over this scale. May be removed later.
    bool        AntiAliasedLines;           
    // Enable anti-aliased lines/borders. Disable if you are really tight on CPU/GPU. Latched at the beginning of the frame (copied to ImDrawList).
    bool        AntiAliasedLinesUseTex;     
    // Enable anti-aliased lines/borders using textures where possible. Require backend to render with bilinear filtering (NOT point/nearest filtering). Latched at the beginning of the frame (copied to ImDrawList).
    bool        AntiAliasedFill;            
    // Enable anti-aliased edges around filled shapes (rounded rectangles, circles, etc.). Disable if you are really tight on CPU/GPU. Latched at the beginning of the frame (copied to ImDrawList).
    float       CurveTessellationTol;       
    // Tessellation tolerance when using PathBezierCurveTo() without a specific number of segments. Decrease for highly tessellated curves (higher quality, more polygons), increase to reduce quality.
    float       CircleTessellationMaxError;
    // Maximum error (in pixels) allowed when using AddCircle()/AddCircleFilled() or drawing rounded corner rectangles with no explicit segment count specified. Decrease for higher quality but more geometry.
    ImVec4      Colors[ImGuiCol_COUNT];

    // Behaviors
    // (It is possible to modify those fields mid-frame if specific behavior need it, unlike e.g. configuration fields in ImGuiIO)
    float             HoverStationaryDelay;     
    // Delay for IsItemHovered(ImGuiHoveredFlags_Stationary). Time required to consider mouse stationary.
    float             HoverDelayShort;          
    // Delay for IsItemHovered(ImGuiHoveredFlags_DelayShort). Usually used along with HoverStationaryDelay.
    float             HoverDelayNormal;         
    // Delay for IsItemHovered(ImGuiHoveredFlags_DelayNormal). "
    ImGuiHoveredFlags HoverFlagsForTooltipMouse;
    // Default flags when using IsItemHovered(ImGuiHoveredFlags_ForTooltip) or BeginItemTooltip()/SetItemTooltip() while using mouse.
    ImGuiHoveredFlags HoverFlagsForTooltipNav;  
    // Default flags when using IsItemHovered(ImGuiHoveredFlags_ForTooltip) or BeginItemTooltip()/SetItemTooltip() while using keyboard/gamepad.

    IMGUI_API ImGuiStyle();
    IMGUI_API void ScaleAllSizes(float scale_factor);
};
```

## 停靠 DrockSpace

### 在viewport停靠

在`NewFrame`后加上`DockSpaceOverViewport`

```c++
ImGui::NewFrame();
ImGui::DockSpaceOverViewport();
// render ui
```

## 高级绘制

### DrawList

`ImDrawList*   GetBackgroundDrawList(ImGuiViewport* viewport = NULL);   ` 获取视口背景层DrawList

`ImDrawList*   GetForegroundDrawList(ImGuiViewport* viewport = NULL); ` 获取视口前景层DrawList

`ImDrawList*   GetWindowDrawList();  ` 获取窗口DrawList

DrawList对象有GDI常用的绘制命令。可以查看源代码DrawList结构体的定义。

## ImGuiFileDialog

https://github.com/aiekick/ImGuiFileDialog

```c++
void drawGui() { 
  // open Dialog Simple
  if (ImGui::Button("Open File Dialog")) {
    IGFD::FileDialogConfig config;
	config.path = ".";
    ImGuiFileDialog::Instance()->OpenDialog("ChooseFileDlgKey", "Choose File", ".cpp,.h,.hpp", config);
  }
  // display
  if (ImGuiFileDialog::Instance()->Display("ChooseFileDlgKey")) {
    if (ImGuiFileDialog::Instance()->IsOk()) { // action if OK
      std::string filePathName = ImGuiFileDialog::Instance()->GetFilePathName();
      std::string filePath = ImGuiFileDialog::Instance()->GetCurrentPath();
      // action
    }
    
    // close
    ImGuiFileDialog::Instance()->Close();
  }
}
```

## Item/Widgets Utilities and Query Functions

```c++
IMGUI_API bool          IsItemHovered(ImGuiHoveredFlags flags = 0);                         // is the last item hovered? (and usable, aka not blocked by a popup, etc.). See ImGuiHoveredFlags for more options.
    IMGUI_API bool          IsItemActive();                                                     // is the last item active? (e.g. button being held, text field being edited. This will continuously return true while holding mouse button on an item. Items that don't interact will always return false)
    IMGUI_API bool          IsItemFocused();                                                    // is the last item focused for keyboard/gamepad navigation?
    IMGUI_API bool          IsItemClicked(ImGuiMouseButton mouse_button = 0);                   // is the last item hovered and mouse clicked on? (**)  == IsMouseClicked(mouse_button) && IsItemHovered()Important. (**) this is NOT equivalent to the behavior of e.g. Button(). Read comments in function definition.
    IMGUI_API bool          IsItemVisible();                                                    // is the last item visible? (items may be out of sight because of clipping/scrolling)
    IMGUI_API bool          IsItemEdited();                                                     // did the last item modify its underlying value this frame? or was pressed? This is generally the same as the "bool" return value of many widgets.
    IMGUI_API bool          IsItemActivated();                                                  // was the last item just made active (item was previously inactive).
    IMGUI_API bool          IsItemDeactivated();                                                // was the last item just made inactive (item was previously active). Useful for Undo/Redo patterns with widgets that require continuous editing.
    IMGUI_API bool          IsItemDeactivatedAfterEdit();                                       // was the last item just made inactive and made a value change when it was active? (e.g. Slider/Drag moved). Useful for Undo/Redo patterns with widgets that require continuous editing. Note that you may get false positives (some widgets such as Combo()/ListBox()/Selectable() will return true even when clicking an already selected item).
    IMGUI_API bool          IsItemToggledOpen();                                                // was the last item open state toggled? set by TreeNode().
    IMGUI_API bool          IsAnyItemHovered();                                                 // is any item hovered?
    IMGUI_API bool          IsAnyItemActive();                                                  // is any item active?
    IMGUI_API bool          IsAnyItemFocused();                                                 // is any item focused?
    IMGUI_API ImGuiID       GetItemID();                                                        // get ID of last item (~~ often same ImGui::GetID(label) beforehand)
    IMGUI_API ImVec2        GetItemRectMin();                                                   // get upper-left bounding rectangle of the last item (screen space)
    IMGUI_API ImVec2        GetItemRectMax();                                                   // get lower-right bounding rectangle of the last item (screen space)
    IMGUI_API ImVec2        GetItemRectSize();                                                  // get size of last item
```

## 拖拽事件

```c++
// Drag and Drop
    // - On source items, call BeginDragDropSource(), if it returns true also call SetDragDropPayload() + EndDragDropSource().
    // - On target candidates, call BeginDragDropTarget(), if it returns true also call AcceptDragDropPayload() + EndDragDropTarget().
    // - If you stop calling BeginDragDropSource() the payload is preserved however it won't have a preview tooltip (we currently display a fallback "..." tooltip, see #1725)
    // - An item can be both drag source and drop target.
    IMGUI_API bool          BeginDragDropSource(ImGuiDragDropFlags flags = 0);                                      // call after submitting an item which may be dragged. when this return true, you can call SetDragDropPayload() + EndDragDropSource()
    IMGUI_API bool          SetDragDropPayload(const char* type, const void* data, size_t sz, ImGuiCond cond = 0);  // type is a user defined string of maximum 32 characters. Strings starting with '_' are reserved for dear imgui internal types. Data is copied and held by imgui. Return true when payload has been accepted.
    IMGUI_API void          EndDragDropSource();                                                                    // only call EndDragDropSource() if BeginDragDropSource() returns true!
    IMGUI_API bool                  BeginDragDropTarget();                                                          // call after submitting an item that may receive a payload. If this returns true, you can call AcceptDragDropPayload() + EndDragDropTarget()
    IMGUI_API const ImGuiPayload*   AcceptDragDropPayload(const char* type, ImGuiDragDropFlags flags = 0);          // accept contents of a given type. If ImGuiDragDropFlags_AcceptBeforeDelivery is set you can peek into the payload before the mouse button is released.
    IMGUI_API void                  EndDragDropTarget();                                                            // only call EndDragDropTarget() if BeginDragDropTarget() returns true!
    IMGUI_API const ImGuiPayload*   GetDragDropPayload();        
```

## 一个绘制树形结构的函数

	void DrawObjTree(const SceneObj& objNode)
	{
		static ImGuiTreeNodeFlags flag = ImGuiTreeNodeFlags_None;
		flag = ImGuiTreeNodeFlags_None;
		
		// 默认展开
		flag |= ImGuiTreeNodeFlags_DefaultOpen;
	
		// 如果没有子节点 设置为叶子节点
		if (!objNode.HasChild())
		{
			flag |= ImGuiTreeNodeFlags_Leaf;
		}
	
		// 如果当前节点为选中节点 设置为选中状态
		if (m_SelectedObj == &objNode)
		{
			flag |= ImGuiTreeNodeFlags_Selected;
		}
	
		if (ImGui::TreeNodeEx(objNode.GetObjName().c_str(), flag))
		{
			if (ImGui::IsItemClicked())
			{ // 点击事件
			
				// 点击当前节点 设置为选中节点
				m_SelectedObj = const_cast<SceneObj*>(&objNode);
				ImGuiIO& io = ImGui::GetIO(); (void)io;
	
				
				if (io.MouseDoubleClicked[0])
				{// 左键双击事件
					
					//双击将镜头对准目标
					if (Camera3D* camera = dynamic_cast<Camera3D*>(Renderer::GetCurrentCamera()))
					{
						camera->HeadTo(m_SelectedObj->GetPosition());
					}
				}
			}
			
			// 如果有子节点 递归
			if (objNode.HasChild())
				for (const SceneObj* obj : objNode.GetChildren())
				{
					DrawObjTree(*obj);
				}
			ImGui::TreePop();
		}
	}

## Item的命名字符串

ImGui的每一个Item实例都有一个字符串名称，ImGui通过这个字符串标识Item实例，那么如果两个同类型的Item有相同的名称就会出错。

在名称字符串前加上`##`，ImGui在绘制ui的时候会隐藏Item名称。

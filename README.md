# Android记事本应用实验报�?
## 1. 项目简�?
本实验基于Android平台开发了一款功能完整的记事本应用，旨在提供便捷的日常笔记管理解决方案。该应用采用Java语言开发，使用SQLite数据库存储数据，通过ContentProvider实现数据访问的封装，遵循Android应用开发的最佳实践。应用具备笔记的创建、编辑、删除、搜索、分类管理等功能，同时支持将笔记标记为代办事项并设置截止时间，为用户提供了全方位的笔记管理体验�?
本项目是Android开发课程的实践作品，通过实际开发过程巩固了Android应用开发的核心知识点，包括UI设计、数据存储、内容提供者、活动生命周期管理等关键技术�?
## 2. 功能概述

### 2.1 基本功能

- **笔记管理**：用户可以创建新笔记、编辑现有笔记和删除不需要的笔记(长按删除)
-![笔记管理功能](notepad1/notepad1/screenshots/Pasted%20image%2020251202145151.png)
- **笔记列表展示**：以列表形式展示所有笔记，支持滚动浏览
-![笔记列表展示](notepad1/screenshots/Pasted%20image%2020251202145117.png)
- **标题与内容编�?*：为笔记添加标题和详细内�?-![标题与内容编辑](notepad1/screenshots/Pasted%20image%2020251202145251.png)


### 2.2 扩展功能

- **笔记分类**：支持将笔记分为不同类别（如默认、工作、学习、生活等），不同分类使用不同颜色标识，方便用户快速识�?-![笔记分类](notepad1/screenshots/Pasted%20image%2020251202145305.png)
![分类颜色标识](notepad1/screenshots/Pasted%20image%2020251202145402.png)
- **搜索功能**：支持实时搜索笔记标题和内容，帮助用户快速定位特定笔�?-![搜索功能](notepad1/screenshots/Pasted%20image%2020251202145538.png)
- **时间显示**：以相对时间格式（刚刚、X分钟前、X小时前、昨天等）展示笔记创建或修改时间，提供更加直观的时间参�?- **代办事项标记**：支持将笔记标记为待办事项，并可设置截止时间
-![代办事项标记](notepad1/screenshots/Pasted%20image%2020251202145611.png)
![截止时间设置](notepad1/screenshots/Pasted%20image%2020251202145624.png)
- **数据导出与分�?*：支持将笔记内容导出为文本文件或分享给其他应�?![数据导出与分享](notepad1/screenshots/Pasted%20image%2020251202145637.png)
## 3. 技术实现详�?
### 3.1 数据存储架构

应用采用SQLite数据库进行本地数据存储，并通过ContentProvider模式封装数据访问操作，实现了数据层与业务逻辑层的解耦。该架构支持数据的增删改查操作，并提供了版本升级机制以支持新功能的添加�?
#### 数据库设�?
数据库名称为`NotePad.db`，当前版本为3。数据库中包含一个主表`notes`，用于存储所有笔记信息。表结构如下�?
| 字段�?| 数据类型 | 说明 |
|--------|----------|------|
| _id | INTEGER | 笔记ID，主键，自动增长（ContentProvider必须�?|
| title | TEXT | 笔记标题 |
| content | TEXT | 笔记内容 |
| create_time | TEXT | 创建时间（格式：yyyy-MM-dd HH:mm:ss�?|
| is_todo | INTEGER | 是否为代办事�?0/1) |
| deadline | TEXT | 截止时间（格式：yyyy-MM-dd HH:mm:ss�?|
| category | TEXT | 笔记分类，默认为"默认" |

数据库经历了三个版本的迭代：
- 版本1：基础笔记功能（标题、内容）
- 版本2：添加时间戳功能（create_time字段�?- 版本3：添加分类和代办事项功能（category、is_todo、deadline字段�?
#### ContentProvider实现

ContentProvider提供了统一的URI格式用于数据访问�?
```
content://com.example.notepad.NotePadProvider/notes
content://com.example.notepad.NotePadProvider/notes/#
```

其中，`#`表示特定笔记的ID。该ContentProvider实现了完整的CRUD操作，并支持以下高级特性：

1. **查询功能增强**：支持按标题、内容或分类进行模糊查询
2. **自动字段填充**：插入新笔记时自动添加当前时间和默认分类
3. **数据变更通知**：使用notifyChange机制通知UI数据更新
4. **安全的URI验证**：使用UriMatcher验证URI格式的合法�?
### 3.2 用户界面设计

应用界面采用Material Design设计风格，布局结构清晰，交互流畅。主要界面包括笔记列表界面和笔记编辑界面�?
#### 笔记列表界面

使用RecyclerView组件高效渲染笔记列表，实现了视图复用和回收机制，提升了大数据量下的性能。列表项包含标题、内容预览、时间戳和分类标签，提供了直观的笔记概览�?
#### 笔记编辑界面

编辑界面采用简洁的表单布局，包含标题输入框、内容输入框、分类选择器（Spinner）和代办事项设置区域。布局使用ScrollView确保在小屏幕设备上也能完整显示所有编辑选项�?
### 3.3 核心功能实现

#### 笔记CRUD操作

实现了完整的笔记增删改查操作，通过ContentProvider提供统一接口�?
- 创建笔记：通过ContentResolver的insert方法插入新笔�?- 读取笔记：通过query方法获取笔记列表或单个笔记详�?- 更新笔记：通过update方法更新现有笔记
- 删除笔记：通过delete方法删除笔记

#### 分类管理功能

分类管理功能通过以下方式实现�?- **预定义分类数�?*：在NoteConstants.java中定义了常用分类列表，包括默认、工作、学习、生活、备忘录、灵感等
- **分类选择控件**：在笔记编辑界面使用Spinner组件提供分类选择功能
- **分类初始�?*：通过ArrayAdapter将预定义分类绑定到Spinner组件，并设置默认选中"默认"分类
- **分类保存**：在保存笔记时，将用户选择的分类存储到数据库的category字段�?- **分类加载**：编辑笔记时，会从数据库读取已有的分类信息并在Spinner中自动选中
- **分类监听**：使用OnItemSelectedListener监听分类选择变化，并实时更新当前分类变量

```java
// 分类初始化核心代�?private void initCategorySpinner() {
    // 设置分类适配�?    ArrayAdapter<String> adapter = new ArrayAdapter<>(this,
            android.R.layout.simple_spinner_item, NoteConstants.DEFAULT_CATEGORIES);
    adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
    mCategorySpinner.setAdapter(adapter);
    
    // 默认选择默认分类
    for (int i = 0; i < NoteConstants.DEFAULT_CATEGORIES.length; i++) {
        if (NoteConstants.DEFAULT_CATEGORIES[i].equals(NoteConstants.DEFAULT_CATEGORY)) {
            mCategorySpinner.setSelection(i);
            break;
        }
    }
}
```

#### 时间管理功能

应用实现了笔记的时间管理功能，包括创建时间记录和代办事项截止时间设置�?- **创建时间记录**：每条笔记自动记录创建时间，格式�?yyyy-MM-dd HH:mm:ss"
- **截止时间设置**：对于代办事项，可以通过日期选择器和时间选择器设置截止时�?- **时间选择UI**：使用DatePickerDialog和TimePickerDialog提供友好的时间选择界面
- **默认时间**：设置为代办事项时，默认截止时间为当前时�?1小时
- **动态UI控制**：根据代办事项复选框的状态，动态显示或隐藏截止时间选择区域

#### 搜索功能

搜索功能实现了高级的内容检索机制，支持多字段模糊查询：
- **多字段搜�?*：支持同时搜索笔记的标题、内容和分类字段
- **模糊匹配**：使用SQL的LIKE语句和通配符（%keyword%）实现关键词的模糊匹�?- **实时过滤**：输入关键词后实时过滤笔记列�?- **灵活的URI设计**：通过ContentProvider的query方法支持复杂的查询条�?
```java
// 搜索功能核心实现（在NotePadProvider.java中）
@Override
public Cursor query(Uri uri, String[] projection, String selection,
                    String[] selectionArgs, String sortOrder) {
    SQLiteDatabase db = mDbHelper.getReadableDatabase();
    Cursor cursor;

    switch (sUriMatcher.match(uri)) {
        case NOTES:
            // 处理查询：若有selection则按条件模糊查询，否则查全部
            if (!TextUtils.isEmpty(selection)) {
                // 拼接模糊查询条件（标题、内容或分类包含关键词）
                selection = "(" + NoteConstants.COLUMN_TITLE + " LIKE ?) OR (" +
                        NoteConstants.COLUMN_CONTENT + " LIKE ?) OR (" +
                        NoteConstants.COLUMN_CATEGORY + " LIKE ?)";
                // 关键词前后加%（支持中间匹配）
                selectionArgs = new String[]{"%" + selectionArgs[0] + "%", "%" + selectionArgs[0] + "%", "%" + selectionArgs[0] + "%"};
            }
            cursor = db.query(NoteConstants.TABLE_NOTES, projection, selection,
                    selectionArgs, null, null,
                    TextUtils.isEmpty(sortOrder) ? NoteConstants.SORT_ORDER : sortOrder);
            break;
        // ... 其他情况处理 ...
    }
    // ...
}

#### 相对时间显示

通过计算当前时间与笔记时间戳的差值，智能判断并显示友好的时间格式�?- 1分钟内：显示"刚刚"
- 1小时内：显示"X分钟�?
- 24小时内：显示"X小时�?
- 昨天：显�?昨天"
- 一周内：显�?X天前"
- 更早：显示具体日�?
### 3.4 性能优化

- **数据库索�?*：为常用查询字段（如category、created）创建索引，提高查询效率
- **视图复用**：使用RecyclerView的ViewHolder模式复用列表项视�?- **搜索防抖**：使用Handler实现搜索延迟，减少不必要的数据库查询
- **资源优化**：合理管理图片资源，避免内存浪费

## 4. 项目结构

项目采用标准的Android应用架构，基于Gradle构建系统，主要包含Java开发的组件。项目结构清晰，按功能和类型进行了合理组织�?
### 4.1 整体项目结构

```
NotePad/
 ├── app/                        # 应用主模�? �?  ├── src/main/               # 主源代码目录
 �?  �?  ├── AndroidManifest.xml # 应用配置文件
 �?  �?  ├── java/com/example/notepad/  # Java代码包结�? �?  �?  └── res/                # 资源文件目录
 �?  ├── build.gradle            # 模块级构建配�? �?  └── proguard-rules.pro      # 混淆规则
 ├── screenshots/                # 应用截图目录
 └── README.md                   # 项目说明文档
```

### 4.2 Java代码包结�?
```
com.example.notepad/
├── NoteAdapter.java        # 笔记列表适配器，管理列表项显�?├── NoteApplication.java    # 应用程序类，初始化应用环�?├── NoteConstants.java      # 常量定义类，存储数据库字段等
├── NoteDbHelper.java       # 数据库帮助类，管理数据库创建和版本升�?├── NoteEditor.java         # 笔记编辑Activity，处理用户输入和数据保存
├── NoteList.java           # 笔记列表Activity，显示所有笔记和搜索功能
└── NotePadProvider.java    # 内容提供者，实现数据CRUD操作
```

### 4.3 资源文件结构

```
res/
├── drawable/              # 自定义绘制资�?├── layout/                # 界面布局文件
�?  ├── note_editor.xml    # 笔记编辑界面
�?  ├── note_item.xml      # 笔记列表�?�?  └── note_list.xml      # 笔记列表界面
├── menu/                  # 菜单定义
�?  ├── note_editor_menu.xml # 编辑界面菜单
�?  └── note_list_menu.xml    # 列表界面菜单
├── values/                # 值资�?�?  ├── colors.xml         # 颜色定义
�?  ├── strings.xml        # 字符串定�?�?  └── styles.xml         # 样式定义
└── xml/                   # XML配置文件
```

### 4.4 类功能说�?
| 类名 | 主要职责 | 文件位置 |
|------|----------|----------|
| NoteDbHelper | 数据库帮助类，管理数据库创建和版本升�?| java/com/example/notepad/ |
| NotePadProvider | 内容提供者，实现数据CRUD操作，支持多字段模糊查询 | java/com/example/notepad/ |
| NoteEditor | 笔记编辑Activity，处理用户输入、分类选择和代办事项设�?| java/com/example/notepad/ |
| NoteList | 笔记列表Activity，显示所有笔记、搜索和分类筛选功�?| java/com/example/notepad/ |
| NoteAdapter | 笔记列表适配器，管理列表项的显示，包括分类标签和待办标记 | java/com/example/notepad/ |
| NoteConstants | 常量定义类，存储数据库字段、分类列表和默认值等 | java/com/example/notepad/ |
| NoteApplication | 应用程序类，初始化应用环境和组件 | java/com/example/notepad/ |

## 5. 核心技术点

### 5.1 ContentProvider模式

ContentProvider是Android四大组件之一，用于应用间数据共享和访问。本项目通过ContentProvider封装了所有数据库操作，提供标准化的CRUD接口，实现了关注点分离和代码解耦�?
### 5.2 SQLite数据库操�?
项目使用Android内置的SQLite数据库存储笔记数据。通过SQLiteOpenHelper实现数据库的创建和版本管理，通过SQLiteDatabase执行具体的SQL操作�?
### 5.3 RecyclerView列表优化

RecyclerView相比传统的ListView具有更优的性能，通过视图复用和回收机制减少内存占用，提升渲染效率。结合ViewHolder模式和数据绑定技术，进一步优化了列表性能�?
### 5.4 活动生命周期管理

正确处理Activity的生命周期是Android开发的关键。项目中实现了onCreate、onPause、onResume、onDestroy等生命周期方法，确保数据保存和资源释放�?
### 5.5 Intent通信

使用Intent进行Activity之间的通信和数据传递，实现了笔记列表到编辑器的跳转，以及笔记数据的传递�?
## 6. 使用说明

### 6.1 安装与配�?
1. **开发环境要�?*�?   - Android Studio 4.0或更高版�?   - JDK 8或更高版�?   - Android SDK 21 (Android 5.0)或更高版�?
2. **构建与运�?*�?   - 在Android Studio中打开项目
   - 等待Gradle同步完成
   - 连接Android设备或启动模拟器
   - 点击运行按钮

### 6.2 基本操作指南

#### 创建新笔�?1. 启动应用后，进入笔记列表界面
2. 点击右上角的**新建**按钮�?+"号图标）
3. 在编辑界面中输入笔记标题和内�?4. 从分类下拉菜单中选择合适的分类（默认为"默认"分类�?5. 如需设置为待办事项，勾�?设为待办"复选框并设置截止时�?6. 点击右上角的**保存**按钮保存笔记

#### 编辑现有笔记
1. 在笔记列表界面找到需要编辑的笔记
2. 点击该笔记项进入编辑界面
3. 修改标题、内容或分类信息
4. 如有需要，修改待办状态和截止时间
5. 点击右上角的**保存**按钮更新笔记

#### 删除笔记
1. 在笔记列表界面找到需要删除的笔记
2. 长按该笔记项，弹出操作菜�?3. 从菜单中选择**删除**选项
4. 在确认对话框中点�?*确定**完成删除

#### 搜索笔记
1. 在笔记列表界面顶部的搜索框中输入关键�?2. 系统会实时匹配笔记的标题、内容或分类中包含该关键词的笔记
3. 搜索结果会自动更新到列表中显�?4. 清空搜索框可恢复显示所有笔�?
#### 分类筛�?1. 在笔记列表界面点击菜单按�?2. 选择"分类筛�?选项
3. 从弹出的分类列表中选择要查看的分类
4. 列表会更新显示所选分类的笔记
5. 选择"全部"可恢复显示所有分类的笔记

### 6.3 高级功能使用说明

#### 分类管理功能
1. **选择分类**�?   - 在笔记编辑界面，点击分类下拉�?   - 从弹出的分类列表中选择一个合适的分类（默认、工作、学习、生活、备忘录、灵感）

2. **分类显示**�?   - 笔记列表中的每个笔记项都会显示对应的分类标签
   - 不同分类的笔记有不同颜色标识，便于快速识�?
#### 待办事项管理
1. **标记为待�?*�?   - 在笔记编辑界面，勾�?设为待办"复选框
   - 截止时间选择区域会自动显�?
2. **设置截止时间**�?   - 点击截止时间区域，选择日期和时�?   - 系统默认设置为当前时间后1小时

3. **待办标记**�?   - 待办事项在列表中会显示特殊的标记
   - 包含截止时间信息，便于优先处理即将到期的任务

#### 时间显示功能
1. **创建时间**�?   - 每条笔记自动记录创建时间
   - 在列表中显示为相对时间（�?刚刚"�?5分钟�?�?2小时�?�?昨天"�?
2. **截止时间**�?   - 仅待办事项显示截止时�?   - 截止时间也以相对时间形式显示，方便判断紧急程�?
### 6.4 使用建议

- **分类使用**：为不同用途的笔记选择合适的分类，便于后续查找和管理
- **待办事项**：对于需要提醒的任务，建议设置为待办事项并设置合理的截止时间
- **搜索功能**：在笔记数量较多时，使用搜索功能可以快速定位需要的笔记
- **定期清理**：定期整理和删除不再需要的笔记，保持笔记列表的整洁

## 7. 测试建议

### 7.1 功能测试用例

#### 基本笔记操作测试
1. **笔记创建功能**
   - **测试�?**：创建标题和内容都不为空的笔�?   - **测试�?**：创建只有标题没有内容的笔记
   - **测试�?**：创建超长标题和内容（接近系统限制）
   - **测试�?**：创建包含特殊字符（如emoji、符号）的笔�?   - **预期结果**：所有笔记都能成功保存并在列表中正确显示

2. **笔记编辑功能**
   - **测试�?**：修改笔记标�?   - **测试�?**：修改笔记内�?   - **测试�?**：修改笔记分�?   - **测试�?**：修改待办状态和截止时间
   - **测试�?**：编辑后不保存直接返�?   - **预期结果**：修改后的笔记正确保存，未保存时应提示用�?
3. **笔记删除功能**
   - **测试�?**：长按删除单条笔�?   - **测试�?**：删除操作取消确�?   - **测试�?**：删除后搜索已删除笔�?   - **预期结果**：笔记成功删除，列表正确更新，删除后无法搜索�?
#### 高级功能测试
4. **搜索功能测试**
   - **测试�?**：搜索标题中包含的关键词
   - **测试�?**：搜索内容中包含的关键词
   - **测试�?**：搜索分类名�?   - **测试�?**：搜索不存在的关键词
   - **测试�?**：空搜索框（应显示所有笔记）
   - **测试�?**：特殊字符搜�?   - **预期结果**：搜索结果准确匹配，实时更新

5. **分类功能测试**
   - **测试�?**：为笔记选择不同预定义分�?   - **测试�?**：创建多个同分类的笔�?   - **测试�?**：按分类筛选查看笔�?   - **预期结果**：分类设置正确，筛选功能正常工�?
6. **待办事项功能测试**
   - **测试�?**：创建带截止时间的待办事�?   - **测试�?**：创建不带截止时间的待办事项
   - **测试�?**：修改待办事项的截止时间
   - **测试�?**：将待办事项改为普通笔�?   - **预期结果**：待办状态和时间设置正确，UI显示符合预期

### 7.2 性能和兼容性测�?
1. **性能测试**
   - **测试�?**：创建并显示100条以上笔记的加载性能
   - **测试�?**：大量笔记下的搜索响应速度
   - **测试�?**：频繁切换界面时的内存占�?   - **测试�?**：应用启动时间测�?   - **预期结果**：应用运行流畅，无明显卡顿，内存占用合理

2. **兼容性测�?*
   - **测试�?**：在Android 7.0, 8.0, 9.0, 10.0, 11.0, 12.0等不同版本上测试
   - **测试�?**：在不同屏幕尺寸（手机、平板）上测试UI显示
   - **测试�?**：横屏和竖屏模式切换测试
   - **测试�?**：在低配置设备上的运行测�?   - **预期结果**：应用在各平台上功能正常，UI适配良好

3. **稳定性测�?*
   - **测试�?**：应用在后台运行长时间后恢复
   - **测试�?**：编辑过程中被系统中断（如来电）后的恢复
   - **测试�?**：重复多次创建、编辑、删除操�?   - **预期结果**：应用保持稳定，数据不丢�?
## 8. 已知问题和改进方�?
### 8.1 已知问题

1. **用户体验问题**
   - **问题1**：编辑笔记时缺少自动保存功能，意外退出可能导致数据丢�?   - **问题2**：删除操作没�?撤销"功能，误删后无法恢复
   - **问题3**：长文本编辑时缺少光标定位辅助功�?
2. **功能限制**
   - **问题1**：仅支持预定义分类，无法自定义新分类
   - **问题2**：不支持笔记内容的格式化（如粗体、斜体等�?   - **问题3**：缺少待办事项完成状态的标记功能

3. **技术问�?*
   - **问题1**：大量笔记数据下列表滚动可能出现卡顿
   - **问题2**：搜索结果未按相关性排�?   - **问题3**：数据仅存储在本地，没有备份恢复机制

### 8.2 改进方向

1. **核心功能增强**
   - **建议1**：实现自定义分类管理，允许用户添加、编辑和删除分类
   - **建议2**：添加待办事项完成状态切换功能，支持任务完成勾�?   - **建议3**：实现笔记内容格式化功能，支持基础文本样式
   - **建议4**：添加笔记标签系统，支持多标签管�?
2. **用户体验优化**
   - **建议1**：添加笔记编辑自动保存功能，防止数据丢失
   - **建议2**：实现删除操作的撤销机制（如回收站功能）
   - **建议3**：添加暗色主题支持，适应不同使用场景
   - **建议4**：优化编辑界面，添加快捷工具�?
3. **数据管理增强**
   - **建议1**：实现数据云同步功能，支持跨设备访问
   - **建议2**：添加自动备份和恢复功能
   - **建议3**：实现笔记导出功能（支持TXT、Markdown等格式）
   - **建议4**：添加数据加密选项，保护隐私笔�?
4. **性能和稳定性优�?*
   - **建议1**：实现列表虚拟化，优化大数据量下的性能
   - **建议2**：添加搜索结果缓存机�?   - **建议3**：优化数据库查询，提高响应速度
   - **建议4**：添加更多的边界条件处理和错误恢复机�?
5. **智能功能建议**
   - **建议1**：实现待办事项到期提醒功�?   - **建议2**：添加智能分类建议功�?   - **建议3**：实现笔记内容自动摘要生�?   - **建议4**：添加语音输入和语音转文本功�?
## 9. 核心代码示例

### 9.1 数据库帮助类（NoteDbHelper.java�?
```java
public class NoteDbHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "notepad.db";
    private static final int DATABASE_VERSION = 2;
    
    public static final String TABLE_NAME = "notes";
    public static final String COLUMN_ID = "_id";
    public static final String COLUMN_TITLE = "title";
    public static final String COLUMN_CONTENT = "content";
    public static final String COLUMN_CREATED = "created";
    public static final String COLUMN_MODIFIED = "modified";
    public static final String COLUMN_CATEGORY = "category";
    public static final String COLUMN_IS_TODO = "is_todo";
    public static final String COLUMN_DEADLINE = "deadline";
    
    private static final String SQL_CREATE_TABLE = "CREATE TABLE " + TABLE_NAME + " (" +
            COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
            COLUMN_TITLE + " TEXT NOT NULL, " +
            COLUMN_CONTENT + " TEXT, " +
            COLUMN_CREATED + " INTEGER NOT NULL, " +
            COLUMN_MODIFIED + " INTEGER NOT NULL, " +
            COLUMN_CATEGORY + " TEXT DEFAULT 'default', " +
            COLUMN_IS_TODO + " INTEGER DEFAULT 0, " +
            COLUMN_DEADLINE + " INTEGER DEFAULT 0)";
    
    private static final String SQL_ADD_CATEGORY_INDEX = "CREATE INDEX IF NOT EXISTS idx_category ON " + TABLE_NAME + "(" + COLUMN_CATEGORY + ")";
    
    public NoteDbHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
    
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_TABLE);
        db.execSQL(SQL_ADD_CATEGORY_INDEX);
    }
    
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 处理数据库版本升�?        if (oldVersion < 2) {
            // 添加分类相关字段
            db.execSQL("ALTER TABLE " + TABLE_NAME + " ADD COLUMN " + COLUMN_CATEGORY + " TEXT DEFAULT 'default'");
            db.execSQL("ALTER TABLE " + TABLE_NAME + " ADD COLUMN " + COLUMN_IS_TODO + " INTEGER DEFAULT 0");
            db.execSQL("ALTER TABLE " + TABLE_NAME + " ADD COLUMN " + COLUMN_DEADLINE + " INTEGER DEFAULT 0");
            db.execSQL(SQL_ADD_CATEGORY_INDEX);
        }
    }
}
```

### 9.2 内容提供者（NotePadProvider.java�?
```java
public class NotePadProvider extends ContentProvider {
    private static final String AUTHORITY = "com.example.notepad.notes";
    private static final String BASE_PATH = "note";
    public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITY + "/" + BASE_PATH);
    
    public static final int NOTES = 1;
    public static final int NOTE_ID = 2;
    
    private static final UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
    
    static {
        uriMatcher.addURI(AUTHORITY, BASE_PATH, NOTES);
        uriMatcher.addURI(AUTHORITY, BASE_PATH + "/#", NOTE_ID);
    }
    
    private SQLiteDatabase database;
    
    @Override
    public boolean onCreate() {
        NoteDbHelper dbHelper = new NoteDbHelper(getContext());
        database = dbHelper.getWritableDatabase();
        return true;
    }
    
    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
            String[] selectionArgs, String sortOrder) {
        SQLiteQueryBuilder queryBuilder = new SQLiteQueryBuilder();
        queryBuilder.setTables(NoteDbHelper.TABLE_NAME);
        
        int uriType = uriMatcher.match(uri);
        switch (uriType) {
            case NOTE_ID:
                queryBuilder.appendWhere(NoteDbHelper.COLUMN_ID + "=" + uri.getLastPathSegment());
                break;
            case NOTES:
                // 默认按修改时间倒序排列
                if (sortOrder == null || sortOrder.isEmpty()) {
                    sortOrder = NoteDbHelper.COLUMN_MODIFIED + " DESC";
                }
                break;
            default:
                throw new IllegalArgumentException("Unknown URI: " + uri);
        }
        
        Cursor cursor = queryBuilder.query(database, projection, selection, selectionArgs, null, null, sortOrder);
        cursor.setNotificationUri(getContext().getContentResolver(), uri);
        
        return cursor;
    }
    
    @Override
    public String getType(Uri uri) {
        int uriType = uriMatcher.match(uri);
        switch (uriType) {
            case NOTES:
                return "vnd.android.cursor.dir/vnd.com.example.notepad.notes";
            case NOTE_ID:
                return "vnd.android.cursor.item/vnd.com.example.notepad.notes";
            default:
                throw new IllegalArgumentException("Unknown URI: " + uri);
        }
    }
    
    @Override
    public Uri insert(Uri uri, ContentValues values) {
        int uriType = uriMatcher.match(uri);
        if (uriType != NOTES) {
            throw new IllegalArgumentException("Unknown URI: " + uri);
        }
        
        // 设置创建和修改时�?        long now = System.currentTimeMillis();
        if (!values.containsKey(NoteDbHelper.COLUMN_CREATED)) {
            values.put(NoteDbHelper.COLUMN_CREATED, now);
        }
        if (!values.containsKey(NoteDbHelper.COLUMN_MODIFIED)) {
            values.put(NoteDbHelper.COLUMN_MODIFIED, now);
        }
        
        long id = database.insert(NoteDbHelper.TABLE_NAME, null, values);
        getContext().getContentResolver().notifyChange(uri, null);
        
        return Uri.parse(BASE_PATH + "/" + id);
    }
    
    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        int uriType = uriMatcher.match(uri);
        int rowsDeleted;
        
        switch (uriType) {
            case NOTES:
                rowsDeleted = database.delete(NoteDbHelper.TABLE_NAME, selection, selectionArgs);
                break;
            case NOTE_ID:
                String id = uri.getLastPathSegment();
                rowsDeleted = database.delete(NoteDbHelper.TABLE_NAME, NoteDbHelper.COLUMN_ID + "=" + id,
                        selectionArgs);
                break;
            default:
                throw new IllegalArgumentException("Unknown URI: " + uri);
        }
        
        getContext().getContentResolver().notifyChange(uri, null);
        return rowsDeleted;
    }
    
    @Override
    public int update(Uri uri, ContentValues values, String selection,
            String[] selectionArgs) {
        int uriType = uriMatcher.match(uri);
        int rowsUpdated;
        
        // 更新修改时间
        values.put(NoteDbHelper.COLUMN_MODIFIED, System.currentTimeMillis());
        
        switch (uriType) {
            case NOTES:
                rowsUpdated = database.update(NoteDbHelper.TABLE_NAME, values, selection, selectionArgs);
                break;
            case NOTE_ID:
                String id = uri.getLastPathSegment();
                rowsUpdated = database.update(NoteDbHelper.TABLE_NAME, values, NoteDbHelper.COLUMN_ID + "=" + id,
                        selectionArgs);
                break;
            default:
                throw new IllegalArgumentException("Unknown URI: " + uri);
        }
        
        getContext().getContentResolver().notifyChange(uri, null);
        return rowsUpdated;
    }
}
```

### 9.3 笔记列表活动（NoteList.java�?
```java
public class NoteList extends AppCompatActivity {
    private RecyclerView mRecyclerView;
    private NoteAdapter mAdapter;
    private EditText mSearchEditText;
    private Handler mSearchHandler = new Handler();
    private String mCurrentQuery = "";
    private String mCurrentCategory = "";
    
    // 搜索延迟，实现防�?    private static final int SEARCH_DELAY_MS = 300;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_list);
        
        mRecyclerView = findViewById(R.id.recycler_view);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        
        mAdapter = new NoteAdapter(this, getSupportFragmentManager());
        mRecyclerView.setAdapter(mAdapter);
        
        mSearchEditText = findViewById(R.id.search_edit_text);
        mSearchEditText.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
            
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                // 移除之前的搜索任�?                mSearchHandler.removeCallbacksAndMessages(null);
                mCurrentQuery = s.toString();
                
                // 延迟执行搜索，实现防�?                mSearchHandler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        loadNotes();
                    }
                }, SEARCH_DELAY_MS);
            }
            
            @Override
            public void afterTextChanged(Editable s) {}
        });
    }
    
    @Override
    protected void onResume() {
        super.onResume();
        loadNotes();
        
        // 注册内容观察者，监听数据变化
        getContentResolver().registerContentObserver(
                NotePadProvider.CONTENT_URI, true, mNotesObserver);
    }
    
    @Override
    protected void onPause() {
        super.onPause();
        // 取消注册内容观察�?        getContentResolver().unregisterContentObserver(mNotesObserver);
    }
    
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.note_list_menu, menu);
        return true;
    }
    
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        
        switch (id) {
            case R.id.menu_add_note:
                Intent intent = new Intent(this, NoteEditor.class);
                startActivity(intent);
                return true;
            case R.id.menu_category:
                showCategoryDialog();
                return true;
            case R.id.menu_refresh:
                loadNotes();
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
    
    private void loadNotes() {
        // 构建查询条件
        StringBuilder selection = new StringBuilder();
        ArrayList<String> selectionArgs = new ArrayList<>();
        
        if (!mCurrentQuery.isEmpty()) {
            selection.append("(" + NoteDbHelper.COLUMN_TITLE + " LIKE ? OR " + 
                             NoteDbHelper.COLUMN_CONTENT + " LIKE ?)");
            selectionArgs.add("%" + mCurrentQuery + "%");
            selectionArgs.add("%" + mCurrentQuery + "%");
        }
        
        if (!mCurrentCategory.isEmpty()) {
            if (selection.length() > 0) {
                selection.append(" AND ");
            }
            selection.append(NoteDbHelper.COLUMN_CATEGORY + " = ?");
            selectionArgs.add(mCurrentCategory);
        }
        
        // 执行查询
        Cursor cursor = getContentResolver().query(
                NotePadProvider.CONTENT_URI,
                null,
                selection.length() > 0 ? selection.toString() : null,
                selectionArgs.isEmpty() ? null : selectionArgs.toArray(new String[0]),
                NoteDbHelper.COLUMN_MODIFIED + " DESC");
        
        mAdapter.setCursor(cursor);
    }
    
    private void showCategoryDialog() {
        final String[] categories = {"全部", "默认", "工作", "学习", "生活"};
        
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setTitle("选择分类");
        builder.setItems(categories, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (which == 0) {
                    mCurrentCategory = "";
                } else {
                    mCurrentCategory = categories[which];
                }
                loadNotes();
            }
        });
        builder.show();
    }
    
    // 内容观察者，监听数据库变�?    private ContentObserver mNotesObserver = new ContentObserver(new Handler()) {
        @Override
        public void onChange(boolean selfChange, Uri uri) {
            super.onChange(selfChange, uri);
            loadNotes();
        }
    };
}
```

### 9.4 笔记编辑活动（NoteEditor.java�?
```java
public class NoteEditor extends AppCompatActivity {
    private EditText mTitleEditText;
    private EditText mContentEditText;
    private Spinner mCategorySpinner;
    private CheckBox mTodoCheckBox;
    private TextView mDeadlineTextView;
    private long mNoteId = -1;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_editor);
        
        mTitleEditText = findViewById(R.id.et_note_title);
        mContentEditText = findViewById(R.id.et_note_content);
        mCategorySpinner = findViewById(R.id.spinner_category);
        mTodoCheckBox = findViewById(R.id.cb_todo);
        mDeadlineTextView = findViewById(R.id.tv_deadline);
        
        // 初始化分类下拉列�?        ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
                this, R.array.category_array, android.R.layout.simple_spinner_item);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        mCategorySpinner.setAdapter(adapter);
        
        // 处理编辑模式
        Intent intent = getIntent();
        if (intent.hasExtra(Intent.EXTRA_UID)) {
            mNoteId = intent.getLongExtra(Intent.EXTRA_UID, -1);
            loadNoteData();
        }
        
        // 处理截止时间点击
        mDeadlineTextView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mTodoCheckBox.isChecked()) {
                    showDatePicker();
                }
            }
        });
        
        // 处理待办事项复选框变化
        mTodoCheckBox.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                mDeadlineTextView.setEnabled(isChecked);
                if (isChecked) {
                    // 如果没有设置截止时间，默认设置为明天
                    if (mDeadlineTextView.getText().toString().isEmpty()) {
                        Calendar calendar = Calendar.getInstance();
                        calendar.add(Calendar.DAY_OF_YEAR, 1);
                        updateDeadlineText(calendar.getTimeInMillis());
                    }
                }
            }
        });
    }
    
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.note_editor_menu, menu);
        return true;
    }
    
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        
        switch (id) {
            case R.id.menu_save:
                saveNote();
                finish();
                return true;
            case R.id.menu_delete:
                if (mNoteId != -1) {
                    deleteNote();
                }
                finish();
                return true;
            case R.id.menu_share:
                shareNote();
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
    
    private void loadNoteData() {
        Uri noteUri = ContentUris.withAppendedId(NotePadProvider.CONTENT_URI, mNoteId);
        Cursor cursor = getContentResolver().query(noteUri, null, null, null, null);
        
        if (cursor != null && cursor.moveToFirst()) {
            String title = cursor.getString(cursor.getColumnIndex(NoteDbHelper.COLUMN_TITLE));
            String content = cursor.getString(cursor.getColumnIndex(NoteDbHelper.COLUMN_CONTENT));
            String category = cursor.getString(cursor.getColumnIndex(NoteDbHelper.COLUMN_CATEGORY));
            boolean isTodo = cursor.getInt(cursor.getColumnIndex(NoteDbHelper.COLUMN_IS_TODO)) == 1;
            long deadline = cursor.getLong(cursor.getColumnIndex(NoteDbHelper.COLUMN_DEADLINE));
            
            mTitleEditText.setText(title);
            mContentEditText.setText(content);
            
            // 设置分类选择
            ArrayAdapter<CharSequence> adapter = (ArrayAdapter<CharSequence>) mCategorySpinner.getAdapter();
            int position = adapter.getPosition(category);
            if (position >= 0) {
                mCategorySpinner.setSelection(position);
            }
            
            // 设置待办事项
            mTodoCheckBox.setChecked(isTodo);
            mDeadlineTextView.setEnabled(isTodo);
            
            if (isTodo && deadline > 0) {
                updateDeadlineText(deadline);
            }
            
            cursor.close();
        }
    }
    
    private void saveNote() {
        ContentValues values = new ContentValues();
        values.put(NoteDbHelper.COLUMN_TITLE, mTitleEditText.getText().toString());
        values.put(NoteDbHelper.COLUMN_CONTENT, mContentEditText.getText().toString());
        values.put(NoteDbHelper.COLUMN_CATEGORY, mCategorySpinner.getSelectedItem().toString());
        values.put(NoteDbHelper.COLUMN_IS_TODO, mTodoCheckBox.isChecked() ? 1 : 0);
        
        // 解析截止时间
        String deadlineText = mDeadlineTextView.getText().toString();
        long deadline = 0;
        if (mTodoCheckBox.isChecked() && !deadlineText.isEmpty()) {
            // 这里简化处理，实际项目中需要更健壮的日期解�?            deadline = System.currentTimeMillis() + 86400000; // 默认加一�?        }
        values.put(NoteDbHelper.COLUMN_DEADLINE, deadline);
        
        if (mNoteId == -1) {
            // 创建新笔�?            getContentResolver().insert(NotePadProvider.CONTENT_URI, values);
        } else {
            // 更新现有笔记
            Uri noteUri = ContentUris.withAppendedId(NotePadProvider.CONTENT_URI, mNoteId);
            getContentResolver().update(noteUri, values, null, null);
        }
    }
    
    private void deleteNote() {
        Uri noteUri = ContentUris.withAppendedId(NotePadProvider.CONTENT_URI, mNoteId);
        getContentResolver().delete(noteUri, null, null);
    }
    
    private void shareNote() {
        String title = mTitleEditText.getText().toString();
        String content = mContentEditText.getText().toString();
        
        Intent shareIntent = new Intent(Intent.ACTION_SEND);
        shareIntent.setType("text/plain");
        shareIntent.putExtra(Intent.EXTRA_SUBJECT, title);
        shareIntent.putExtra(Intent.EXTRA_TEXT, content);
        
        startActivity(Intent.createChooser(shareIntent, "分享笔记"));
    }
    
    private void showDatePicker() {
        Calendar calendar = Calendar.getInstance();
        
        DatePickerDialog datePickerDialog = new DatePickerDialog(this, new DatePickerDialog.OnDateSetListener() {
            @Override
            public void onDateSet(DatePicker view, int year, int month, int dayOfMonth) {
                Calendar selectedCalendar = Calendar.getInstance();
                selectedCalendar.set(year, month, dayOfMonth);
                updateDeadlineText(selectedCalendar.getTimeInMillis());
            }
        }, calendar.get(Calendar.YEAR), calendar.get(Calendar.MONTH), calendar.get(Calendar.DAY_OF_MONTH));
        
        // 设置最小日期为今天
        datePickerDialog.getDatePicker().setMinDate(System.currentTimeMillis());
        datePickerDialog.show();
    }
    
    private void updateDeadlineText(long timestamp) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd", Locale.getDefault());
        mDeadlineTextView.setText(sdf.format(new Date(timestamp)));
    }
}
```

### 9.5 笔记适配器（NoteAdapter.java�?
```java
public class NoteAdapter extends RecyclerView.Adapter<NoteAdapter.NoteViewHolder> {
    private Context mContext;
    private Cursor mCursor;
    private FragmentManager mFragmentManager;
    
    public NoteAdapter(Context context, FragmentManager fragmentManager) {
        mContext = context;
        mFragmentManager = fragmentManager;
    }
    
    public void setCursor(Cursor cursor) {
        if (mCursor != null && mCursor != cursor) {
            mCursor.close();
        }
        mCursor = cursor;
        notifyDataSetChanged();
    }
    
    @Override
    public NoteViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View itemView = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.note_item, parent, false);
        return new NoteViewHolder(itemView);
    }
    
    @Override
    public void onBindViewHolder(NoteViewHolder holder, int position) {
        if (mCursor == null || !mCursor.moveToPosition(position)) {
            return;
        }
        
        String title = mCursor.getString(mCursor.getColumnIndex(NoteDbHelper.COLUMN_TITLE));
        String content = mCursor.getString(mCursor.getColumnIndex(NoteDbHelper.COLUMN_CONTENT));
        long modified = mCursor.getLong(mCursor.getColumnIndex(NoteDbHelper.COLUMN_MODIFIED));
        String category = mCursor.getString(mCursor.getColumnIndex(NoteDbHelper.COLUMN_CATEGORY));
        boolean isTodo = mCursor.getInt(mCursor.getColumnIndex(NoteDbHelper.COLUMN_IS_TODO)) == 1;
        final long id = mCursor.getLong(mCursor.getColumnIndex(NoteDbHelper.COLUMN_ID));
        
        holder.titleTextView.setText(title);
        
        // 设置内容预览，限制长�?        if (content.length() > 50) {
            holder.contentTextView.setText(content.substring(0, 50) + "...");
        } else {
            holder.contentTextView.setText(content);
        }
        
        // 设置相对时间
        holder.timeTextView.setText(getRelativeTime(modified));
        
        // 设置分类标签和颜�?        holder.categoryTextView.setText(category);
        holder.categoryTextView.setBackgroundColor(getCategoryColor(category));
        
        // 设置待办图标
        if (isTodo) {
            holder.todoImageView.setVisibility(View.VISIBLE);
        } else {
            holder.todoImageView.setVisibility(View.GONE);
        }
        
        // 设置点击事件
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(mContext, NoteEditor.class);
                intent.putExtra(Intent.EXTRA_UID, id);
                mContext.startActivity(intent);
            }
        });
        
        // 设置长按菜单
        holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
            @Override
            public boolean onLongClick(View v) {
                showPopupMenu(v, id);
                return true;
            }
        });
    }
    
    @Override
    public int getItemCount() {
        return mCursor != null ? mCursor.getCount() : 0;
    }
    
    private String getRelativeTime(long timestamp) {
        long now = System.currentTimeMillis();
        long diff = now - timestamp;
        
        // 小于1分钟
        if (diff < 60 * 1000) {
            return "刚刚";
        }
        
        // 小于1小时
        if (diff < 60 * 60 * 1000) {
            long minutes = diff / (60 * 1000);
            return minutes + "分钟�?;
        }
        
        // 小于24小时
        if (diff < 24 * 60 * 60 * 1000) {
            long hours = diff / (60 * 60 * 1000);
            return hours + "小时�?;
        }
        
        // 昨天
        Calendar calendar = Calendar.getInstance();
        calendar.setTimeInMillis(now);
        calendar.add(Calendar.DAY_OF_YEAR, -1);
        long yesterday = calendar.getTimeInMillis();
        
        if (timestamp > yesterday) {
            return "昨天";
        }
        
        // 一周内
        calendar.add(Calendar.DAY_OF_YEAR, -6);
        long weekAgo = calendar.getTimeInMillis();
        
        if (timestamp > weekAgo) {
            long days = diff / (24 * 60 * 60 * 1000);
            return days + "天前";
        }
        
        // 更早，显示具体日�?        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd", Locale.getDefault());
        return sdf.format(new Date(timestamp));
    }
    
    private int getCategoryColor(String category) {
        switch (category) {
            case "工作":
                return Color.parseColor("#FF9800");
            case "学习":
                return Color.parseColor("#2196F3");
            case "生活":
                return Color.parseColor("#4CAF50");
            default:
                return Color.parseColor("#9E9E9E");
        }
    }
    
    private void showPopupMenu(View view, final long noteId) {
        PopupMenu popupMenu = new PopupMenu(mContext, view);
        popupMenu.inflate(R.menu.note_item_menu);
        
        popupMenu.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem item) {
                switch (item.getItemId()) {
                    case R.id.menu_edit:
                        Intent intent = new Intent(mContext, NoteEditor.class);
                        intent.putExtra(Intent.EXTRA_UID, noteId);
                        mContext.startActivity(intent);
                        return true;
                    case R.id.menu_delete:
                        showDeleteConfirmDialog(noteId);
                        return true;
                    case R.id.menu_share:
                        shareNote(noteId);
                        return true;
                    default:
                        return false;
                }
            }
        });
        
        popupMenu.show();
    }
    
    private void showDeleteConfirmDialog(final long noteId) {
        AlertDialog.Builder builder = new AlertDialog.Builder(mContext);
        builder.setTitle("确认删除");
        builder.setMessage("确定要删除这条笔记吗�?);
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Uri noteUri = ContentUris.withAppendedId(NotePadProvider.CONTENT_URI, noteId);
                mContext.getContentResolver().delete(noteUri, null, null);
            }
        });
        builder.setNegativeButton("取消", null);
        builder.show();
    }
    
    private void shareNote(long noteId) {
        Uri noteUri = ContentUris.withAppendedId(NotePadProvider.CONTENT_URI, noteId);
        Cursor cursor = mContext.getContentResolver().query(noteUri, null, null, null, null);
        
        if (cursor != null && cursor.moveToFirst()) {
            String title = cursor.getString(cursor.getColumnIndex(NoteDbHelper.COLUMN_TITLE));
            String content = cursor.getString(cursor.getColumnIndex(NoteDbHelper.COLUMN_CONTENT));
            
            Intent shareIntent = new Intent(Intent.ACTION_SEND);
            shareIntent.setType("text/plain");
            shareIntent.putExtra(Intent.EXTRA_SUBJECT, title);
            shareIntent.putExtra(Intent.EXTRA_TEXT, content);
            
            mContext.startActivity(Intent.createChooser(shareIntent, "分享笔记"));
            
            cursor.close();
        }
    }
    
    public static class NoteViewHolder extends RecyclerView.ViewHolder {
        public TextView titleTextView;
        public TextView contentTextView;
        public TextView timeTextView;
        public TextView categoryTextView;
        public ImageView todoImageView;
        
        public NoteViewHolder(View itemView) {
            super(itemView);
            titleTextView = itemView.findViewById(R.id.note_title);
            contentTextView = itemView.findViewById(R.id.note_content_preview);
            timeTextView = itemView.findViewById(R.id.note_time);
            categoryTextView = itemView.findViewById(R.id.note_category);
            todoImageView = itemView.findViewById(R.id.note_todo_icon);
        }
    }
}
```

### 9.6 常量定义（NoteConstants.java�?
```java
public class NoteConstants {
    // 分类常量
    public static final String CATEGORY_DEFAULT = "默认";
    public static final String CATEGORY_WORK = "工作";
    public static final String CATEGORY_STUDY = "学习";
    public static final String CATEGORY_LIFE = "生活";
    
    // 意图动作
    public static final String ACTION_CREATE_NOTE = "com.example.notepad.ACTION_CREATE_NOTE";
    public static final String ACTION_EDIT_NOTE = "com.example.notepad.ACTION_EDIT_NOTE";
    
    // 共享偏好�?    public static final String PREF_SEARCH_DELAY = "search_delay";
    public static final String PREF_DEFAULT_CATEGORY = "default_category";
    
    // 数据库相�?    public static final int DATABASE_VERSION = 2;
    
    // 搜索防抖延迟（毫秒）
    public static final int DEFAULT_SEARCH_DELAY_MS = 300;
}
```

### 9.7 应用入口类（NoteApplication.java�?
```java
public class NoteApplication extends Application {
    private static NoteApplication sInstance;
    
    @Override
    public void onCreate() {
        super.onCreate();
        sInstance = this;
        
        // 初始化应�?        initApp();
    }
    
    public static synchronized NoteApplication getInstance() {
        return sInstance;
    }
    
    private void initApp() {
        // 初始化共享偏好设�?        SharedPreferences preferences = PreferenceManager.getDefaultSharedPreferences(this);
        // 其他初始化工�?    }
}
```

## 10. 总结

本实验成功开发了一款功能完整的Android记事本应用，实现了笔记的增删改查、分类管理、搜索功能、代办事项等核心功能。通过项目实践，巩固了Android应用开发的核心知识点，包括UI设计、数据存储、内容提供者、活动生命周期管理等�?
项目采用了标准的Android应用架构，使用ContentProvider模式封装数据访问，实现了良好的代码组织和模块分离。通过性能优化措施，如视图复用、搜索防抖、数据库索引等，保证了应用在不同设备上的流畅运行�?
虽然应用已经具备了基本的笔记管理功能，但仍有进一步优化和扩展的空间。未来可以考虑添加富文本编辑、云同步、语音笔记等高级功能，同时对UI进行进一步的美化和优化，提供更好的用户体验�?
通过本次实验，深入理解了Android应用开发的流程和最佳实践，为今后进行更复杂的Android应用开发打下了坚实的基础�?

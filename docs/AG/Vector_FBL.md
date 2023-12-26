## FBL软件架构
![软件架构](../pics/screenshot-20231019-154513.png)
### FBL的memory configuration

#### 1. Flash Block Table

   物理存储地址与逻辑地址的映射，包含起止地址，Memory Device索引与Logical Block索引

   ![image-20231019142940311](../pics/image-20231019142940311.png)

#### 2. Logical Block Table

   逻辑地址

#### 3. Memory Device Table

   定义所有的memory devices，包括内部flash，EEPROM以及外部flash设备



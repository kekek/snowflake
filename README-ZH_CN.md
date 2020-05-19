snowflake
====

[English](README.md)

### ID Format
ID 格式从原始的Twitter snowflake 演变过来， 修改后的格式为
* 整个ID是一个存储在int64中的42位整数
* 31 bits 用来存事件戳, 使用用户自定义纪元【epoch】.
* 2 bits 用来存机器位 - 范围从 0 到 4.
* 8 bits 用来存随机数 - 范围从 0 到 255.

### 自定义纪元【Epoch】
默认情况下，此程序包使用1288834974的Twitter纪元或2010年11月4日01:42:54。您可以通过将snownow.Epoch设置为以0.1秒为单位的时间来设置自己的纪元值。

### 自定说明
设置自定义历元或位值时，需要在调用雪花包上的任何函数（包括NewNode（））之前进行设置。否则，您设置的自定义值将无法正确应用。

### 如何生成.
每次您生成ID时，它都将像这样工作。
* 使用ID的31位存储毫秒精度的时间戳。
* 然后将NodeID添加到后续位中。
* 然后添加序列号，从0开始，并针对每0.1秒中生成的每个ID递增。如果您在同一个0.1秒内生成了足够的ID，以至于序列将滚动或溢出，则generate函数将暂停直到下一个毫秒。

修改后的 id format 如下：
```
+--------------------------------------------------------------------------+
| 1 Bit Unused | 31 Bit Timestamp |  2 Bit NodeID  |   8 Bit Sequence ID |
+--------------------------------------------------------------------------+
```

使用默认设置，每个节点上， 每0.1秒生成 256个 唯一的id
## 开始使用

### 安装

假设您已经有一个可以运行的Go环境，否则请参见[golang](https://golang.org/doc/install).

```sh
go get github.com/kekek/snowflake
```


### 使用

将包导入到项目中，然后使用唯一的节点号构造一个新的snowflake节点。默认设置允许节点号范围为0到4。如果您设置了自定义NodeBits值，则需要计算节点号范围。使用节点对象，调用Generate（）方法来生成并返回唯一的 snowflake ID。

请记住，即使跨多个服务器，您创建的每个节点也必须具有唯一的节点号。如果您不保持节点号唯一，则生成器无法保证所有节点上的唯一ID。

** example:**

```go
package main

import (
	"fmt"

	"github.com/kekek/snowflake"
)

func main() {

	// Create a new Node with a Node number of 1
	node, err := snowflake.NewNode(1)
	if err != nil {
		fmt.Println(err)
		return
	}

	// Generate a snowflake ID.
	id := node.Generate()

	// Print out the ID in a few different ways.
	fmt.Printf("Int64  ID: %d\n", id)
	fmt.Printf("String ID: %s\n", id)
	fmt.Printf("Base2  ID: %s\n", id.Base2())
	fmt.Printf("Base64 ID: %s\n", id.Base64())

	// Print out the ID's timestamp
	fmt.Printf("ID Time  : %d\n", id.Time())

	// Print out the ID's node number
	fmt.Printf("ID Node  : %d\n", id.Node())

	// Print out the ID's sequence number
	fmt.Printf("ID Step  : %d\n", id.Step())

  // Generate and print, all in one.
  fmt.Printf("ID       : %d\n", node.Generate().Int64())
}
```

## 修改说明

当前使用 github.com/bwmarrin/snowflake 生成的 ID 整数值已超出js 解析的整数范围，因此通过通过简单修改， 使生成的id满足业务集群较小的服务
同时，能够js 能够解析的范围 

## 申明
 
本库修改自 [github.com/bwmarrin/snowflake](github.com/bwmarrin/snowflake)， 感谢作者的贡献， 如有侵权，请联系本人删除

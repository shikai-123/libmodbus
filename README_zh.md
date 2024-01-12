# 前言
**Read this in other languages: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [中文](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**
**其他语言版本: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [中文](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**

# 介绍

**这是一个libmodbus分支，主要是增加了“报文记录”功能。他不会被合并的主分支上。
如果你是第一次使用libmodbus，我不建议你使用这个库**

# 函数
**函数命名规则就是在原先的函数的名字加上_msgs。**
```cpp
//这是库本身读取功能码3的函数
MODBUS_API int modbus_read_registers(
    modbus_t *ctx, int addr, int nb, uint16_t *dest);
 
/*
这是新增函数，用来接收报文；
参数：
    sbuf:下发报文
    slen:下发报文长度
    rbuf:上送报文
    rlen:上送报文长度（当接收报文失败，长度为0）
返回值：
    保持和libmodbus一致
*/
MODBUS_API int modbus_read_registers_msgs(
    modbus_t *ctx, int addr, int nb, uint16_t *dest,
    uint8_t *sbuf, int *slen, uint8_t *rbuf, int *rlen);
```

# 分支介绍
我适配了不同modbus版本，放在了不同的分支中，你可以根据你的需要下载。

## 主分支：只是我fork的开源作者的仓库的主分支罢了。
该分支，不支持本文说的功能。

## 分支1：modbus_3.1.2_RTUoverTCP_msg
首先modbus版本是3.1.2
这个分支的特点是：
1、支持RTU跑在TCP链接连接上。
2、记录报文

## 分支2：modbus_3.1.6_unify_file_registers_msg
首先modbus版本是3.1.6
这个分支的特点是支持功能码20、21.
1、支持功能码20、21。
2、记录报文

## 分支3：modbus_3.1.10_msg
这个分支，只是目前最新主干，基于这个做了一个报文记录


## demo

``` cpp
int test01()
{
	modbus_t *ctx;
	ctx = modbus_new_rtu("COM3", 9600, 'N', 8, 1);
	if (ctx == NULL) {
		fprintf(stderr, "Unable to create the libmodbus context\n");
		return -1;
	}
	modbus_set_response_timeout(ctx, 0, 999999);
 
	modbus_set_slave(ctx, 1);
	//modbus_set_debug(ctx, 1);
	modbus_set_error_recovery(
		ctx,
		MODBUS_ERROR_RECOVERY_LINK
	);
	if (modbus_connect(ctx) == -1) {
		fprintf(stderr, "Connection failed: %s\n", modbus_strerror(errno));
		modbus_free(ctx);
		return -1;
	}
 
	uint16_t dest[20] = { 0 };
	uint8_t sbuf[20] = { 0 };
	int slen = 0;
	uint8_t rbuf[20] = { 0 };
	int rlen = 0;
 
	modbus_read_registers_msgs(ctx, 1, 1, dest, sbuf, &slen, rbuf, &rlen);
	// modbus_read_registers(ctx, 1, 1, dest);
	// modbus_read_bits(ctx, 11, 10, sbuf);
	printf("Connection failed: %s\n", modbus_strerror(errno));
 
	printf("____\n");
	for (size_t i = 0; i < slen; i++)
	{
		printf("%x ", (uint8_t*)sbuf[i]);
	}
	printf("\n");
	for (size_t i = 0; i < rlen; i++)
	{
		printf("%x ", (uint8_t*)rbuf[i]);
	}
 
	std::cout << slen << " " << rlen << std::endl;
 
 
	modbus_close(ctx);
	modbus_free(ctx);
}

结果：
Connection failed: No error
____
1 3 0 1 0 1 d5 ca
1 3 2 0 0 b8 44 8 7
```
	
	
	
## 参考：
	具体可以参考：https://blog.csdn.net/weixin_46537765/article/details/135554297



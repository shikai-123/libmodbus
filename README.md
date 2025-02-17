# Preface
**Read this in other languages: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [中文](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**
**其他语言版本: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [中文](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**

# Introduction

**This is a branch of libmodbus that primarily adds the "message logging" feature. It will not be merged into the main branch. If you are using libmodbus for the first time, I do not recommend using this library.**

# Functions
**The function naming convention is to add "_msgs" to the original function name.**
```cpp
//This is the original function in the library for reading function code 3
MODBUS_API int modbus_read_registers(
    modbus_t *ctx, int addr, int nb, uint16_t *dest);
 
/*
This is the newly added function for receiving messages;
Parameters:
    sbuf: Downlink message
    slen: Length of the downlink message
    rbuf: Uplink message
    rlen: Length of the uplink message (0 when receiving message fails)
Return:
    Maintained consistency with libmodbus
*/
MODBUS_API int modbus_read_registers_msgs(
    modbus_t *ctx, int addr, int nb, uint16_t *dest,
    uint8_t *sbuf, int *slen, uint8_t *rbuf, int *rlen);
```

# Branches Overview
I have adapted different versions of modbus and placed them in different branches, which you can download according to your needs.

## Main Branch: Just the main branch of the repository I forked from the open-source author.
In this branch, the mentioned feature is not supported.

## Branch 1: modbus_3.1.2_RTUoverTCP_msg
Firstly, the modbus version is 3.1.2.
Features of this branch include:

Support for RTU over TCP connections.
Message logging.

## Branch 2: modbus_3.1.6_unify_file_registers_msg
Firstly, the modbus version is 3.1.6.
Features of this branch include support for function codes 20 and 21.

Support for function codes 20 and 21.
Message logging.

## Branch 3: modbus_3.1.10_msg
This branch is based on the latest mainline as of now (3.1.10) and has message logging implemented.


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

Result:
Connection failed: No error
____
1 3 0 1 0 1 d5 ca
1 3 2 0 0 b8 44 8 7
```
	
	
	
## Reference:
For more details, you can refer to:：https://blog.csdn.net/weixin_46537765/article/details/135554297

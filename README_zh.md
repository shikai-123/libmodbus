# ǰ��
**Read this in other languages: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [����](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**
**�������԰汾: [English](https://github.com/shikai-123/libmodbus/blob/master/README.md), [����](https://github.com/shikai-123/libmodbus/blob/master/README_zh.md).**

# ����

**����һ��libmodbus��֧����Ҫ�������ˡ����ļ�¼�����ܡ������ᱻ�ϲ�������֧�ϡ�
������ǵ�һ��ʹ��libmodbus���Ҳ�������ʹ�������**

# ����
**�����������������ԭ�ȵĺ��������ּ���_msgs��**
```cpp
//���ǿⱾ���ȡ������3�ĺ���
MODBUS_API int modbus_read_registers(
    modbus_t *ctx, int addr, int nb, uint16_t *dest);
 
/*
���������������������ձ��ģ�
������
    sbuf:�·�����
    slen:�·����ĳ���
    rbuf:���ͱ���
    rlen:���ͱ��ĳ��ȣ������ձ���ʧ�ܣ�����Ϊ0��
����ֵ��
    ���ֺ�libmodbusһ��
*/
MODBUS_API int modbus_read_registers_msgs(
    modbus_t *ctx, int addr, int nb, uint16_t *dest,
    uint8_t *sbuf, int *slen, uint8_t *rbuf, int *rlen);
```

# ��֧����
�������˲�ͬmodbus�汾�������˲�ͬ�ķ�֧�У�����Ը��������Ҫ���ء�

## ����֧��ֻ����fork�Ŀ�Դ���ߵĲֿ������֧���ˡ�
�÷�֧����֧�ֱ���˵�Ĺ��ܡ�

## ��֧1��modbus_3.1.2_RTUoverTCP_msg
����modbus�汾��3.1.2
�����֧���ص��ǣ�
1��֧��RTU����TCP���������ϡ�
2����¼����

## ��֧2��modbus_3.1.6_unify_file_registers_msg
����modbus�汾��3.1.6
�����֧���ص���֧�ֹ�����20��21.
1��֧�ֹ�����20��21��
2����¼����

## ��֧3��modbus_3.1.10_msg
�����֧��ֻ��Ŀǰ�������ɣ������������һ�����ļ�¼


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

�����
Connection failed: No error
____
1 3 0 1 0 1 d5 ca
1 3 2 0 0 b8 44 8 7
```
	
	
	
## �ο���
	������Բο���https://blog.csdn.net/weixin_46537765/article/details/135554297



# 数据库-查询MySQL，返回json文件
```python
import pymysql # 下载pymysql模块 pip install PyMySQL
import json
import decimal

# Decimal类型在json中不能序列化，需要做处理
class DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, decimal.Decimal):
            return float(o)
        super(DecimalEncoder, self).default(o)

try:
    # 连接数据库
    connect = pymysql.connect(
        host='rm-8vb6ft839d98p65k1ko.mysql.zhangbei.rds.aliyuncs.com',
        user='dwreaduser',
        password='dwread3306#@!',
        db='desp_ecologyenv_dw',
        charset='utf8')
    cursor = connect.cursor()
    # 写sql语句
    # 获取返回结果,取相同地点的第一条数据
    # 替换null值为'N/A',其他值也会转换为字符，很难受
    # 可以添加判断条件，得到符合的结果
    sql = '''select 
        STNM,
        (CASE when (CODCR is null) then 'N/A' else CODCR end) as CODCR,
        (CASE when (TN is null) then 'N/A' else TN end) as TN,
        (CASE when (NH3N is null) then 'N/A' else NH3N end) as NH3N,
        (CASE when (TP is null) then 'N/A' else TP end) as TP
        from ml_water_quality_monitor_info group by STNM'''
    cursor.execute(sql)
    results = cursor.fetchmany(-1) # 参数为返回results中的元素数量(默认为1，-1指所有)

    # 构造数据对象，格式化输出
    datas = []
    section = {}
    for r in results:
        data = {}
        data['STNM'] = r[0]
        data['CODCR'] = r[1]
        data['TN'] = r[2]
        data['NH3N'] = r[3]
        data['TP'] = r[4]
        datas.append(data)
    
    section['data'] = datas

    # cls是指定序列化方法，ensure_ascii=False输出中文字符，ident格式化输出
    jsondata = json.dumps(section, cls=DecimalEncoder,ensure_ascii=False,indent=4)

    with open("./test.json", "w") as f:
        f.write(jsondata)

    cursor.close()
    connect.close()

except pymysql.Error:
    print("Mysql Error")
```
运行python文件后，可以得到一个json文件(都是字符形式的)
```none
{
    "data": [
        {
            "STNM": "安州闸（国控）",
            "CODCR": "21.0",
            "TN": "N/A",
            "NH3N": "1.47",
            "TP": "0.310"
        },
        {
            "STNM": "八大淀",
            "CODCR": "28.0",
            "TN": "N/A",
            "NH3N": "0.11",
            "TP": "0.070"
        },
        {
            "STNM": "白洋淀码头",
            "CODCR": "36.0",
            "TN": "N/A",
            "NH3N": "0.10",
            "TP": "0.060"
        },
        ...
    ]
}
```

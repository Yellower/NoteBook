# logging模块的使用

```python
def get_logger(log_file):
    """
    创建logger，用于记录日志到文件和输出
    :param logfile : 日志文件的路径
    """
    logger = logging.getLogger()
    logger.setLevel(logging.DEBUG) # 日志总等级，控制需要那些日志，不同等级的日志会根据handler的等级来分发

    # 文件handler
    fh = logging.FileHandler(log_file, mode='w')
    fh.setLevel(logging.DEBUG) # fh的等级，控制那些日志写入文件
    # 文件handler的输出格式
    formatter = logging.Formatter("%(asctime)s - %(levelname)s %(message)s")
    fh.setFormatter(formatter)
    logger.addHandler(fh)

    # 控制台输出handler
    ch = logging.StreamHandler()
    ch.setLevel(logging.INFO) # ch的等级，控制那些日志输出到控制台
    ch.setFormatter(formatter)
    logger.addHandler(ch)

    return logger

if __name__ == '__main__':
    logger = get_logger('log.log')
    logger.debug('debug message')
    logger.info('info message')
    logger.warning('warning message')
    logger.error('error message')
    logger.critical('critical message')
```


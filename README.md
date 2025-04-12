# tarea-3_mapededuce
# Análisis de Logs Web con Hadoop MapReduce en Python

## 1. Archivo de Logs de Ejemplo (`access_log.txt`)

El mismo archivo de logs que antes:

```
access_log.txt
--------------
192.168.1.105 - - [10/Oct/2023:08:15:32 -0500] "GET /index.html HTTP/1.1" 200 1256
192.168.1.110 - - [10/Oct/2023:08:15:40 -0500] "GET /images/logo.png HTTP/1.1" 200 5432
192.168.1.105 - - [10/Oct/2023:08:16:12 -0500] "GET /about.html HTTP/1.1" 200 3421
192.168.1.120 - - [10/Oct/2023:08:16:45 -0500] "GET /contact.php HTTP/1.1" 200 2314
192.168.1.110 - - [10/Oct/2023:08:17:01 -0500] "GET /products/1 HTTP/1.1" 200 8765
192.168.1.105 - - [10/Oct/2023:08:17:23 -0500] "GET /favicon.ico HTTP/1.1" 404 217
192.168.1.130 - - [10/Oct/2023:08:18:05 -0500] "POST /login.php HTTP/1.1" 302 1234
192.168.1.105 - - [10/Oct/2023:08:18:30 -0500] "GET /admin/dashboard HTTP/1.1" 403 1123
192.168.1.110 - - [10/Oct/2023:08:19:12 -0500] "GET /products/2 HTTP/1.1" 200 6543
192.168.1.120 - - [10/Oct/2023:08:20:01 -0500] "GET /index.html HTTP/1.1" 200 1256
```

## 2. Código para MapReduce (`weblog_analyzer.py`)

```python
#!/usr/bin/env python3

from mrjob.job import MRJob
import re

class WebLogAnalyzer(MRJob):

def mapper(self, _, line):
# Expresión regular para parsear logs en Common Log Format
log_pattern = r'^(\S+) (\S+) (\S+) \[([^]]+)\] "(\S+) (\S+) (\S+)" (\d+) (\d+)$'
match = re.match(log_pattern, line)

if match:
ip = match.group(1)
resource = match.group(6)
status = match.group(8)

# Emitir estadísticas por IP
yield f"IP:{ip}", 1

# Emitir estadísticas por recurso
yield f"RESOURCE:{resource}", 1

# Emitir estadísticas por código de estado
yield f"STATUS:{status}", 1

def reducer(self, key, values):
yield key, sum(values)

if __name__ == '__main__':
WebLogAnalyzer.run()
```

## 3. README.md Actualizado 

```markdown
## Cómo levantar el nodo de Hadoop
```bash
# Iniciar servicios HDFS y YARN
start-dfs.sh
start-yarn.sh

# Verificar servicios
jps
```
## Cómo apagar el nodo de Hadoop
```bash
stop-yarn.sh
stop-dfs.sh

# Verificar que todos los procesos se detuvieron
jps
```



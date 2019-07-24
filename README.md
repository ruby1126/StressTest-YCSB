# StressTest-YCSB 壓力測試-YCSB

使用YCSB壓測mongodb

### 下載YCSB 
<ol>
<li>Download the latest release of YCSB</li>
<pre><code>git clone git://github.com/brianfrankcooper/YCSB.git
cd YCSB
</code></pre>
<li>編譯mongodb
<pre><code>mvn -pl com.yahoo.ycsb:mongodb-binding -am clean package -DskipTests
</code></pre>
(編譯全部的指令:mvn clean package)
(DskipTests參數代表跳過測試)
</li>
</ol>

### 建構測試
<ol>
<li>構造測試數據，YCSB會基於參數設定，往數據庫裡面構造測試需要的數據(powershell執行)
<pre><code>bin/ycsb load mongodb-async -s -P workloads/workloada > outputLoad.txt</code></pre>
ex:
<pre><code>bin/ycsb load mongodb -s -P workloads/workloada -p mongodb.url=mongodb://帳號:密碼@127.0.0.1:27017
bin/ycsb run mongodb -s -P workloads/workloada -p mongodb.url=mongodb://帳號:密碼@127.0.0.1:27017 > outputRun.txt
</code></pre>
"load"是在數據庫中創建數據。"run"是對數據庫中可用數據執行語句，可能包括讀取，插入和更新，具體取決於workloada的屬性。
</li>
<li>Example</li>
<pre><code>bin/ycsb run mongodb -s -P workloads/workloadf -p mongodb.url=mongodb://帳號:密碼@127.0.0.1:27017/?waitQueueMultiple=5000000  -threads 50000 -p operationcount=50000 
</code></pre>
</ol>

### 檔案說明

<pre>
bin：
	- 目錄下有個可執行的YCSB文件，是個蟒腳本，是用戶操作的命令行接口.ycsb主邏輯是：解析命令行，設置的java環境，加載Java的庫，封裝成可以執行的Java的命令，並執行

workloads：
	- 目錄下有各種workload的模板，可以基於workload進行個性化修改

  workloada：重更新負載：50/50% 混合讀/寫
	workloadb：讀為主負載：95/5% 混合讀/寫
	workloadc：只讀：100%讀
	workloadd：最新讀負載：更多流量在當前插入
	workloade：短距離：短程查詢
	workloadf：讀-修改-寫：讀、修改和更新存在的記錄
		  
workload配置:
	fieldcount: 每條記錄字段個數 (default: 10)
	fieldlength: 每個字段長度 (default: 100)
	readallfields: 是否讀取所有字段true或者讀取一個字段false (default: true)
	readproportion: 讀取作業比例 (default: 0.95)
	updateproportion: 更新作業比例 (default: 0.05)
	insertproportion: 插入作業比例 (default: 0)
	scanproportion: 掃描作業比例 (default: 0)
	readmodifywriteproportion: 讀取一條記錄修改它並寫回的比例 (default: 0)
	requestdistribution: 請求的分佈規則 uniform, zipfian or latest (default: uniform)
	maxscanlength: 掃描作業最大記錄數 (default: 1000)
	scanlengthdistribution: 在1和最大掃描記錄數的之間的分佈規則 (default: uniform)
	insertorder: 記錄被插入的規則ordered或者hashed (default: hashed)
	operationcount: 執行的操作數.
	maxexecutiontime: 執行操作的最長時間，當然如果沒有超過這個時間以運行時間為主。
	table: 測試表的名稱 (default: usertable)
	recordcount: 加載到數據庫的紀錄條數 (default: 0)

core：
	- 包含YCSB裡各種核心實現，比如DB的虛擬類DB.java，各個分貝子類都要繼承該類;還有比如workload抽象類，如果我們要自定義workload實現也需要繼承該類

各種DB的目錄：
	- 比如mongo，redis等，裡面包含了對應測試的原始碼等。
	- 當ycsb mvn編譯後，會在對應的目錄下生成target文件，ycsb會加載對應target文件中的class
</pre>

### 參數
<pre>
  -threads n: execute using n threads (default: 1) - can also be specified as the "threadcount" property using -p 使用n個線程執行
  -target n: attempt to do n operations per second (default: unlimited) - can also be specified as the "target" property using -p 嘗試每秒行n次操作（默認值：無限制）
  -load:  run the loading phase of the workload 運行工作負載的加載階段
  -t:  run the transactions phase of the workload (default) 運行工作負載的事務階段（默認）
  -db dbname: specify the name of the DB to use (default: com.yahoo.ycsb.BasicDB) - can also be specified as the "db" property using -p 指定要使用的DB的名稱（默認值：com.yahoo.ycsb.BasicDB） - 也可以使用-p指定為“db”屬性
  -P propertyfile: load properties from the given file. Multiple files can be specified, and will be processed in the order specified 從給定文件加載屬性。 可以指定多個文件，並按指定的順序處理
  -p name=value:  specify a property to be passed to the DB and workloads;multiple properties can be specified, and override any values in the propertyfile 定要傳遞給DB和工作負載的屬性;可以指定多個屬性，並覆蓋屬性文件中的任何值
  -s:  show status during run (default: no status) 運行期間顯示狀態（默認值：無狀態）
  -l label:  use label for status (e.g. to label one experiment out of a whole batch) 使用標籤作為狀態（例如，標記整批中的一個實驗）
</pre>
### 結果說明
<pre>
[OVERALL] 區顯示測試總體情況
	[OVERALL], RunTime(ms), 3509 #運行總時間
	[OVERALL], Throughput(ops/sec), 284.9814762040467 #吞吐量，每秒操作數
[TOTAL_GC*]區顯示垃圾回收情況
	[TOTAL_GCS_Copy], Count, 36 #總複製次數
	[TOTAL_GC_TIME_Copy], Time(ms), 160 #複製時間
	[TOTAL_GC_TIME_%_Copy], Time(%), 4.559703619264748 #複製時間百分比
	[TOTAL_GCS_MarkSweepCompact], Count, 2  #MarkSweepCompact回收次數
	[TOTAL_GC_TIME_MarkSweepCompact], Time(ms), 41 #MarkSweepCompact回收時間
	[TOTAL_GC_TIME_%_MarkSweepCompact], Time(%), 1.1684240524365916 #MarkSweepCompact回收時間百分比
	[TOTAL_GCs], Count, 38 #全局GC次數
	[TOTAL_GC_TIME], Time(ms), 201 #全局GC時間
	[TOTAL_GC_TIME_%], Time(%), 5.72812767170134 #全局GC時間百分比
[READ]讀取操作
	[READ], Operations, 1000 #總操作數
	[READ], AverageLatency(us), 223493.5 #平均延遲（微秒）
	[READ], MinLatency(us), 655 #最小延遲
	[READ], MaxLatency(us), 1593343 #最大延遲
	[READ], 95thPercentileLatency(us), 1414143 #第95百分位延遲
	[READ], 99thPercentileLatency(us), 1453055 #第99百分位延遲
	[READ], Return=OK, 1000 #結果(正確)，總操作數
[READ-MODIFY-WRITE]讀-修改-寫
	[READ-MODIFY-WRITE], Operations, 495 #總操作數
	[READ-MODIFY-WRITE], AverageLatency(us), 242816.8 #平均延遲（微秒）
	[READ-MODIFY-WRITE], MinLatency(us), 1262 #最小延遲
	[READ-MODIFY-WRITE], MaxLatency(us), 1596415 #最大延遲
	[READ-MODIFY-WRITE], 95thPercentileLatency(us), 1563647 #第95百分位延遲
	[READ-MODIFY-WRITE], 99thPercentileLatency(us), 1587199 #第99百分位延遲
[CLEANUP]清理操作
	[CLEANUP], Operations, 1000 #總操作數
	[CLEANUP], AverageLatency(us), 6.598 #平均延遲（微秒）
	[CLEANUP], MinLatency(us), 0 #最小延遲
	[CLEANUP], MaxLatency(us), 6507 #最大延遲
	[CLEANUP], 95thPercentileLatency(us), 1 #第95百分位延遲
	[CLEANUP], 99thPercentileLatency(us), 2 #第99百分位延遲
[UPDATE]更新操作
	[UPDATE], Operations, 495 #總操作數
	[UPDATE], AverageLatency(us), 13431.274747474747 #平均延遲（微秒）
	[UPDATE], MinLatency(us), 568 #最小延遲
	[UPDATE], MaxLatency(us), 477695 #最大延遲
	[UPDATE], 95thPercentileLatency(us), 60191 #第95百分位延遲
	[UPDATE], 99thPercentileLatency(us), 335871 #第99百分位延遲
	[UPDATE], Return=OK, 495 #結果(正確)，總操作數
</pre>

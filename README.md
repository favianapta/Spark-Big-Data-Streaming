# Spark-Big-Data-Streaming

<a name="readme-top"></a>

<!-- network_wordcount.py -->
# Network Wordcount

  ### Kode
  
  ```css
  from __future__ import print_function

  import sys
  from pyspark import SparkContext
  from pyspark.streaming import StreamingContext
  
  if __name__ == "__main__":
      if len(sys.argv) != 3:
          print("Usage: network_wordcount.py <hostname> <port>", file=sys.stderr)
          exit(-1)
      sc = SparkContext(appName="PythonStreamingNetworkWordCount")
      ssc = StreamingContext(sc, 1)
  
      lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
      counts = lines.flatMap(lambda line: line.split(" "))\
                    .map(lambda word: (word, 1))\
                    .reduceByKey(lambda a, b: a+b)
      counts.pprint()
  
      ssc.start()
      ssc.awaitTermination()
  ```
     
  ### HASIL
  
  <img src="images/network_wordcount.png" width="60%" height="60%">
  
  ### PENJELASAN

  + Penjelasan
    - sys.argv <br>
        > sys.argv adalah list pada python yang berisi semua perintah pada command-line.

    - sys.stderr <br>
        > sys.stderr mencetak langsung ke konsol berupa pesan pengecualian (exception) dan kesalahan
        
    - StreamingContext <br>
        > StreamingContext mewakili koneksi ke cluster Spark dan dapat digunakan untuk membuat berbagai sumber input DStream
        
    - sc <br>
        > Default dari PySpark SparkContext

    - socketTextStream <br>
        > Buat input dari nama host sumber TCP: port. Data diterima menggunakan soket TCP dan menerima byte ditafsirkan sebagai UTF8 yang disandikan \n baris yang dibatasi.

    - reduceByKey <br>
        > Transformasi digunakan untuk menggabungkan nilai setiap kunci menggunakan fungsi pengurangan asosiatif pada PySpark RDD.

    - awaitTermination <br>
        > Menunggu penghentian kueri ini, baik dengan kueri. stop() atau dengan pengecualian. Jika kueri telah diakhiri dengan pengecualian, maka pengecualian akan dilemparkan.


<!-- Stateful Stream.py -->
# Stateful Stream

  ### Kode
  
  ```css
  from __future__ import print_function

  import sys
  
  from pyspark import SparkContext
  from pyspark.streaming import StreamingContext
  
  if __name__ == "__main__":
      if len(sys.argv) != 3:
          print("Usage: stateful_network_wordcount.py  ", file=sys.stderr)
          exit(-1)
      sc = SparkContext(appName="PythonStreamingStatefulNetworkWordCount")
      ssc = StreamingContext(sc, 5)
      ssc.checkpoint("checkpoint")
  
      # RDD with initial state (key, value) pairs
      initialStateRDD = sc.parallelize([(u'hello', 1), (u'world', 1)])
  
      def updateFunc(new_values, last_sum):
          return sum(new_values) + (last_sum or 0)
  
      lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
      running_counts = lines.flatMap(lambda line: line.split(" "))\
                            .map(lambda word: (word, 1))\
                            .updateStateByKey(updateFunc, initialRDD=initialStateRDD)
  
      running_counts.pprint()
  
      ssc.start()
      ssc.awaitTermination()
  ```
     
  ### HASIL
  
  <img src="images/Stateful Stream.png" width="60%" height="60%">
  
  ### PENJELASAN

  + Penjelasan
    - nc <br>
        > Utilitas nc (atau netcat) digunakan untuk apa saja di bawah matahari yang melibatkan TCP atau UDP. Itu dapat membuka koneksi TCP, mengirim paket UDP, mendengarkan port TCP dan UDP yang sewenang-wenang, melakukan pemindaian port, dan menangani IPv4 dan IPv6

    - lk <br>
        > -l adalah untuk listen pada port, sedangkan -k untuk menjaga agar listener terbuka
        
    - spark-submit <br>
        > Skrip spark-submit di direktori bin Spark digunakan untuk meluncurkan aplikasi di cluster DStream
        
    - master <br>
        > URL master untuk kluster

    - local[*] <br>
        > Menjalankan Spark secara lokal dengan thread pekerja sebanyak inti logis di mesin Anda.

    - ssc.checkpoint <br>
        > Mengatur direktori pos pemeriksaan

    - parallelize <br>
        > PySpark parallelize() adalah fungsi di SparkContext dan digunakan untuk membuat RDD dari kumpulan daftar

    - updateStateByKey <br>
        > Kembalikan DStream "status" baru di mana status untuk setiap kunci diperbarui dengan menerapkan fungsi yang diberikan pada status kunci sebelumnya dan nilai baru untuk kunci tersebut. Ini dapat digunakan untuk memelihara data status arbitrer untuk setiap kunci.

    - flatMap <br>
        > flatMap() adalah operasi transformasi yang meratakan RDD/DataFrame (array/memetakan kolom DataFrame) setelah menerapkan fungsi pada setiap elemen dan mengembalikan PySpark RDD/DataFrame baru.

    - rdd.take(5) <br>
        > Ambil 5 elemen pertama dari RDD

    - transform <br>
        > digunakan untuk menerapkan transformasi pada kolom bertipe Array.

    - rdd.sortByKey(False) <br>
        > Sort dengan descending order
  

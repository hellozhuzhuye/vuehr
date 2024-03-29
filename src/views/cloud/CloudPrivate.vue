<template>
<div>
  <h1>Minio纠删码快速入门 <a href="https://slack.min.io"><img src="https://slack.min.io/slack?type=svg" alt="Slack"></a></h1>

  <p>Minio使用纠删码<code>erasure code</code>和<code>checksum</code>来保护数据免受硬件故障和无声数据损坏。 即便您丢失一半数量（N/2）的硬盘，您仍然可以恢复数据。</p>

  <h2>什么是纠删码<code>erasure code</code>?</h2>

  <p>纠删码是一种恢复丢失和损坏数据的数学算法， Minio采用里德-所罗门码将对象分片为数据和奇偶校验块。 这就意味着如果是12块盘，一个对象可被分片的范围是：6个数据块和6个奇偶校验块 到 10个数据块和2个奇偶校验块之间。</p>

  <p>默认情况下, MinIO 将对象拆分成N/2数据和N/2 奇偶校验盘. 虽然你可以通过 <a href="https://github.com/minio/minio/tree/master/docs/zh_CN/erasure/storage-class">存储类型</a> 自定义配置, 但是我们还是推荐N/2个数据和奇偶校验块, 因为它可以确保对硬盘故障提供最佳保护。</p>

  <p>比如上面12个盘的例子，通过默认配置运行MinIO服务的话，你可以丢失任意6块盘（不管其是存放的数据块还是奇偶校验块），你仍可以从剩下的盘中的数据进行恢复，是不是很NB，感兴趣的同学请翻墙google。</p>

  <h2>为什么纠删码有用?</h2>

  <p>纠删码的工作原理和RAID或者复制不同，像RAID6可以在损失两块盘的情况下不丢数据，而Minio纠删码可以在丢失一半的盘的情况下，仍可以保证数据安全。 而且Minio纠删码是作用在对象级别，可以一次恢复一个对象，而RAID是作用在卷级别，数据恢复时间很长。 Minio对每个对象单独编码，存储服务一经部署，通常情况下是不需要更换硬盘或者修复。Minio纠删码的设计目标是为了性能和尽可能的使用硬件加速。

  <p>位衰减又被称为数据腐化<code>Data Rot</code>、无声数据损坏<code>Silent Data Corruption</code>,是目前硬盘数据的一种严重数据丢失问题。硬盘上的数据可能会神不知鬼不觉就损坏了，也没有什么错误日志。正所谓明枪易躲，暗箭难防，这种背地里犯的错比硬盘直接咔咔宕了还危险。 不过不用怕，Minio纠删码采用了高速 <a href="https://github.com/minio/highwayhash">HighwayHash</a> 基于哈希的校验和来防范位衰减。</p>

  <h2>驱动器（盘）如何使用纠删码?</h2>

  <p>MinIO会把你提供的所有驱动器，按照<em>4 到 16</em>个一组划分为多个纠删码集合，因此，你提供的驱动器数量必须是以上这些数字(4到16)的倍数。每个对象都会被写入一个单独的纠删码集合中。</p>

  <p>Minio会尽可能使用最大的纠删码集合大小（EC set size）进行划分.比如 <em>18个盘</em>会被划分为2个纠删码集合，每个集合有9个盘；<em>24个盘</em>也会被划分为2个纠删码集合，每个集合有12个盘。对于将MinIO作为独立的纠删码部署运行的场景而言，这是正确的。然而，在 <a href="https://docs.minio.io/cn/distributed-minio-quickstart-guide.html">分布式部署</a> 时，选择的是基于节点（亲和力）的纠删条带大小.</p>

  <p>驱动器的大小应当都差不多。</p>

  <h2>Minio纠删码快速入门</h2>

  <h3>1. 前提条件:</h3>

  <p>安装Minio- <a href="https://docs.min.io/cn/minio-quickstart-guide">Minio快速入门</a></p>

  <h3>2. 以纠删码模式运行Minio</h3>

  <p>示例: 使用Minio，在12个盘中启动Minio服务。</p>

  <pre lang="sh"><code>minio server /data{1...12}
</code></pre>

  <p>示例: 使用Minio Docker镜像，在8块盘中启动Minio服务。</p>

  <pre lang="sh"><code>docker run -p 9000:9000 --name minio \
  -v /mnt/data1:/data1 \
  -v /mnt/data2:/data2 \
  -v /mnt/data3:/data3 \
  -v /mnt/data4:/data4 \
  -v /mnt/data5:/data5 \
  -v /mnt/data6:/data6 \
  -v /mnt/data7:/data7 \
  -v /mnt/data8:/data8 \
  minio/minio server /data{1...8}
</code></pre>

  <h3>3. 验证是否设置成功</h3>

  <p>你可以随意拔掉硬盘，看Minio是否可以正常读写。</p>
</div>
</template>

<script>
export default {
name: "CloudPrivate"
}
</script>

<style scoped>

</style>
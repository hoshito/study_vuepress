# Vueを埋め込む

このページのmdファイルと比較しながら見てみると良い。

<div v-for="page in allPage">
  <a :href="page.path">{{ page.title }}</a><br>
  {{ page }}
  <hr>
</div>

<script>
export default {
  computed: {
    allPage: function() {
      return this.$site.pages.sort((a, b) => a.title > b.title ? 1 : -1)
    }
  }
}
</script>

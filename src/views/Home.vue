<template>
  <div class="home" v-loading="loading">
    <el-row>
      <el-image
              :src="cherryezLogo"
              alt="cherryez"
              fit="scale-down"
              id="centerLog"
              lazy
              style="height: 150px;width: 150px;"
      >
        <div class="image-slot" slot="error">
          <i class="el-icon-picture-outline"></i>
        </div>
        <div class="image-slot" slot="placeholder">
          车厘子互联
        </div>
      </el-image>
    </el-row>

    <div class="c-divider-sm"></div>

    <el-row>
      <h3>车厘子API接口文档</h3>
    </el-row>

    <div class="c-divider-def"></div>

    <el-row>
      <el-col>
        <el-tooltip
                content="需要带上协议http://或https://"
                effect="light"
                placement="bottom"
        >
          <el-input
                  placeholder="请输入api文档接口Url路径"
                  style="width: 600px"
                  v-model="api_docs_url"
          >
            <el-button
                    @click="getApi"
                    icon="el-icon-position"
                    slot="append"
            ></el-button>
          </el-input>
        </el-tooltip>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  /* eslint-disable no-unused-vars */

  var _this;
export default {
  name: "Home",
  components: {},
  mounted() {
    _this = this;
  },
  data() {
    return {
      cherryezLogo: "https://qcdn.cherryez.com/logo/cherryez/onlyCherry.png",
      api_docs_url: "",
      loading: false
    };
  },
  methods: {
    getApi() {
      _this.loading = true;
      var docsUrl = _this.api_docs_url;

      _this.axios
              .request({
                url: docsUrl
              })
              .then(function (resp) {
                var jsonData = resp.data;

                _this.$router.push({
                  name: "Docs",
                  params: {
                    jsonData: jsonData
                  }
                });
              })
              .catch(function (error) {
                _this.$notify({
                  title: "提示",
                  type: "warning",
                  message: "你输入的url可能有误",
                  position: "bottom-right"
                });
              })
              .finally(function () {
                _this.loading = false;
              });
    }
  }
};
</script>
<style lang="scss" scoped>
  @import "src/assets/css/style";

  .home {
    width: 100%;
    height: 100%;
    position: absolute;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: $color-grey;
  }
</style>

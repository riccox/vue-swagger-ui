<template>
    <div id="Docs">
        <el-container>
            <el-header id="docs-header">
                <div class="item ">
                    <h2>
                        {{ jsonData.info.title }}

                        <el-tag
                                effect="dark"
                                size="mini"
                                style="margin: 5px"
                                type="warning"
                        >
                            {{ jsonData.info.version }}
                        </el-tag>
                    </h2>
                </div>

                <div class="item" style="font-size: medium">
                    {{ jsonData.info.description }}
                </div>

                <div class="item">
                    HOST: <i>{{ jsonData.host }}</i>
                </div>

                <div class="item">作者 {{ jsonData.info.contact.name }}</div>

                <div class="item">
                    网址
                    <el-link :href="jsonData.info.contact.url">
                        {{ jsonData.info.contact.url }}
                    </el-link>
                </div>

                <div class="item">
                    <el-link
                            :href="'mailTo://' + jsonData.info.contact.email"
                            icon="el-icon-message"
                    >
                        {{ jsonData.info.contact.email }}
                    </el-link>
                </div>

                <div class="item">
                    <el-link :href="jsonData.info.license.url" icon="el-icon-position"
                    >{{ jsonData.info.license.name }}
                    </el-link>
                </div>
            </el-header>

            <el-container>
                <el-aside>
                    <el-menu :default-active="activeTagIndex" @select="clickMenu">
                        <el-menu-item
                                :index="index"
                                :key="tag.name"
                                v-for="(tag, index) in jsonData.tags"
                        >
                            <i class="el-icon-menu"></i>
                            <span slot="title">{{ tag.name }}</span>
                        </el-menu-item>
                    </el-menu>
                </el-aside>

                <el-main>
                    <el-collapse accordion v-model="activePathIndex">
                        <el-collapse-item
                                :key="index"
                                :name="index"
                                class="info_item"
                                v-for="(pathInfo, index) in tags[activeTagIndex].pathInfos"
                        >
                            <template slot="title">
                                <el-tag
                                        :type="getMethodColor(pathInfo.method)"
                                        effect="dark"
                                        style="margin: 0 10px;font-size: 20px"
                                >
                                    {{ pathInfo.method.toUpperCase() }}
                                </el-tag>
                                <p style="font-size: 20px">{{ pathInfo.summary }}</p>
                            </template>

                            <el-row>
                                <h1>{{ pathInfo.pathStr }}</h1>
                            </el-row>

                            <el-row style="font-size: 17px" v-html="pathInfo.description">
                            </el-row>

                            <el-row>
                                <el-table
                                        :data="pathInfo.parameters"
                                        border
                                        size="small"
                                        stripe
                                        style="width: 100%;font-size: 15px"
                                >
                                    <el-table-column label="参数位置" prop="in" width="100">
                                    </el-table-column>
                                    <el-table-column label="参数名" prop="name" width="130">
                                    </el-table-column>
                                    <el-table-column label="参数类型" prop="type" width="90">
                                    </el-table-column>
                                    <el-table-column label="必填" prop="required" width="60">
                                    </el-table-column>
                                    <el-table-column label="描述" prop="description">
                                    </el-table-column>
                                </el-table>
                            </el-row>
                        </el-collapse-item>
                    </el-collapse>
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>

<script>
    // eslint-disable-next-line no-unused-vars
    var _this;
    export default {
        name: "Docs",
        mounted() {
            _this = this;

            this.jsonData = this.$route.params.jsonData;
            this.pathNames = this.getObjectKeys(this.jsonData.paths);

            if (_this.pathNames.length == 0) {
                _this.$message.warning("该接口文档无可用接口！");
            }
            console.log(this.jsonData);
            _this.tags = _this.transfer(_this.jsonData);
            console.log(_this.tags);
        },
        data() {
            return {
                jsonData: {},
                activePathIndex: "0",
                activeTagIndex: "0",
                pathNames: [],
                activePathNames: [],
                tags: []
            };
        },
        methods: {
            goBack() {
                _this.$router.back();
            },
            clickMenu(index) {
                _this.activeTagIndex = index;
            },
            getObjectKeys(object) {
                var keys = [];
                for (var property in object) keys.push(property);
                return keys;
            },
            getMethodColor(method) {
                switch (method) {
                    case "get":
                        return "primary";
                    case "post":
                        return "success";
                    case "put":
                        return "warning";
                    case "delete":
                        return "danger";
                    case "patch":
                        return "info";
                }
            },
            transfer(json) {
                var tags = json.tags;
                var paths = json.paths;

                var pathNames = this.getObjectKeys(paths);

                var pathInfos = [];

                for (var i = 0; i < pathNames.length; i++) {
                    var pathName = pathNames[i];

                    var requestMethods = _this.getObjectKeys(paths[pathName]);

                    for (var n = 0; n < requestMethods.length; n++) {
                        var pathInfo = paths[pathName][requestMethods[n]];
                        pathInfo.method = requestMethods[n];
                        pathInfo.pathStr = pathName;
                        pathInfos.push(pathInfo);
                    }
                }
                var res = [];
                for (var m = 0; m < tags.length; m++) {
                    var tag = tags[m];
                    var tagPathInfos = [];

                    for (var z = 0; z < pathInfos.length; z++) {
                        var info = pathInfos[z];

                        if (info.tags.indexOf(tag.name) != -1) {
                            tagPathInfos.push(info);
                        }
                    }

                    tag.pathInfos = tagPathInfos;
                    res.push(tag);
                }
                return res;
            }
        }
    };
</script>

<style lang="scss" scoped>
    @import "src/assets/css/style";

    #Docs {
    }

    #docs-header {
        display: flex;
        flex-direction: row;
        justify-content: space-around;
        align-items: center;
        background-color: $color-blue-light;

        div {
            padding: 5px 0px;
        }
    }

    .info_item {
        padding: 5px;

        div {
            padding: 5px;
        }
    }

    .clearfix:before,
    .clearfix:after {
        display: table;
        content: "";
    }

    .clearfix:after {
        clear: both;
    }
</style>

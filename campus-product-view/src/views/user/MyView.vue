<template>
    <div class="product-list">
        <div class="clear-message">
            <span @click="clearView">
                <i class="el-icon-s-open"></i>
            </span>
        </div>
        <el-row v-if="productList.length === 0">
            <el-empty description="浏览记录为空"></el-empty>
        </el-row>
        <el-row v-else>
            <el-col :span="6" v-for="(product, index) in productList" :key="index">
                <div class="item-product">
                    <div class="cover">
                        <img :src="coverListParse(product)" alt="" srcset="">
                    </div>
                    <div style="display: flex;justify-content: left;gap: 4px;align-items: center;">
                        <span class="bargain-hover">{{ product.isBargain ? '支持砍价' : '不支持砍价' }}</span>
                        <span class="title" @click="route(product)">
                            {{ product.name }}
                        </span>
                    </div>
                    <div style="padding-block: 15px;">
                        <span class="decimel-symbol">¥</span>
                        <span class="price">{{ product.price }}</span>
                    </div>
                </div>
            </el-col>
        </el-row>
    </div>
</template>
<script>
export default {
    name: 'MyProduct',
    data() {
        return {
            productList: []
        };
    },
    created() {
        this.fetchProduct();
    },
    methods: {
        clearView() {
            this.$axios.post(`/interaction/batchDeleteView`).then(res => {
                const { data } = res; // 解构
                if (data.code === 200) {
                    this.$notify({
                        duration: 1000,
                        title: '足迹清除成功',
                        message: data.msg,
                        type: 'success'
                    });
                    this.fetchProduct();
                }
            }).catch(error => {
                console.log("商品足迹清除异常：", error);
            })
        },
        route(product) {
            // 跳转商品详情
            this.$router.push('/product-detail?productId=' + product.id);
        },
        /**
         * 商品封面图处理
         * 从字符串转成可用数组
         * @param {*} product 待处理商品信息
         */
        coverListParse(product) {
            if (product.coverList === null) {
                return;
            }
            const newCoverList = product.coverList.split(',');
            return newCoverList[0];
        },
        /**
         * 查询用户自己发布的商品信息
         */
        fetchProduct() {
            this.$axios.post('/interaction/myView').then(res => {
                const { data } = res; // 解构
                if (data.code === 200) {
                    this.productList = data.data;
                }
            }).catch(error => {
                console.log("商品查询异常：", error);
            })
        },
    }
};
</script>
<style scoped lang="scss">
.product-list {
    padding-block: 20px;

    .clear-message {
        margin-block: 10px;
        display: flex;
        justify-content: right;

        span:hover {
            background-color: rgb(241, 241, 241);
        }

        span {
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background-color: rgb(246, 246, 246);
        }
    }

    .item-product {
        padding: 10px 10px 16px 10px;
        box-sizing: border-box;
        border-radius: 15px;
        transition: all .5s;
        cursor: pointer;

        .cover {
            img {
                width: 100%;
                height: 240px;
                border-radius: 10px;
            }
        }

        .bargain-hover {
            font-size: 12px;
            font-weight: 800;
            background-color: rgb(255, 230, 15);
            color: rgb(51, 51, 51);
            border-radius: 2px;
            padding: 2px 6px;
        }

        .title {
            font-size: 20px;
            color: #1f1f1f;
        }

        .decimel-symbol {
            font-size: 14px;
            color: #ff4f24;
            font-weight: 800;
        }

        .price {
            font-size: 24px;
            color: #ff4f24;
            font-weight: 800;
            margin-right: 6px;
        }

        .love {
            font-size: 14px;
            color: #999;
        }

    }

    .item-product:hover {
        box-shadow: 1px 4px 6px rgba(0, 0, 0, 0.1), 0 1px 3px rgba(0, 0, 0, 0.06);
    }
}
</style>
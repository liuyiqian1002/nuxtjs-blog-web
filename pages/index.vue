<template>
  <div>
    <ul
      v-if="article && article.data && article.data.length > 0"
      class="response-wrap article"
    >
      <li v-for="item in article.data" :key="item.id" class="article-list">
        <div class="article-item">
          <div class="article-image">
            <img :src="item.img_url" :alt="item.title" />
          </div>

          <div class="article-item-content">
            <h1 class="article-title" @click="(e) => jumpURL(e, item.id)">
              {{ item.title }}
            </h1>
            <div class="article-description">
              {{ item.description }}
            </div>

            <div class="article-category">
              {{ item.category_info ? item.category_info.name : '' }}
            </div>
          </div>
        </div>
      </li>
    </ul>
    <div v-else class="empty-data">
      暂无数据
      <a v-if="isClear" href="/">清空搜索条件</a>
    </div>

    <div v-if="isLoad" class="response-wrap more" @click="loadMore">
      <div class="more-text">点击加载更多</div>
      <div class="more-arrow">
        <img src="https://cdn.boblog.com/arrow.png" alt="" />
      </div>
    </div>
  </div>
</template>
<script>
import { mapState } from 'vuex'
import { getArticleList } from '@/request/api/article'
export default {
  name: 'HomeIndex',
  async asyncData(context) {
    const { id, keyword, category_id: categoryId, page = 1 } = context.query

    const [err, res] = await getArticleList({
      id,
      category_id: categoryId,
      keyword,
      page,
      is_category: 1,
      is_admin: 1,
    })

    if (!err) {
      const isLoad = res.data.data.meta.total_pages > page
      return {
        isClear: !!keyword || !!categoryId,
        page,
        isLoad,
        categoryId,
        article: res.data.data,
      }
    }else {
      return  {
        isClear:  false,
        page: 1,
        isLoad: false,
        categoryId : "",
        article: null,
      }
    }
  },
  async fetch({ store }) {
    await store.dispatch('category/getCategoryData')
  },
  head() {
    return {
      title: 'QQ客栈 - qqinns.com - 个人博客 - 千千客栈',
      meta: [
        {
          name: 'keywords',
          content:
            'QQ,博客,个人博客,千千客栈,梁凤波,qq,blog,qqblog,qqinns，前端开发工程师,前端性能优化,JavaScript,css,html',
        },
        {
          name: 'description',
          content: '个人博客 - 千千客栈 - qqinns.com，专注于前端开发技术，前端性能优化！',
        },
      ],
    }
  },
  computed: {
    ...mapState({
      categoryList: (state) => state.category.categoryList,
    }),
    // 是否为空数据
    isEmptyData() {
      return (
        this.article &&
        Array.isArray(this.article.data) &&
        this.article.data.length === 0
      )
    },
  },
  beforeDestroy() {
    if(this.progress) {
      this.progress.removeProgress()
      this.progress = null
    }

  },
  mounted() {
    this.$nextTick(() => {
      const ProgressIndicator = require('@/lib/progress-indicator')
      // eslint-disable-next-line no-new
      this.progress = new ProgressIndicator()
    })
  },
  methods: {
    jumpURL(e, id) {
      e.preventDefault()
      this.$router.push('/article?id=' + id)
    },
    // 获取新数据
    async fetchData(id) {
      const [err, res] = await getArticleList({
        category_id: id,
        is_category: 1,
        is_admin: 1,
        page: this.page,
      })
      if (!err) {
        this.categoryId = id
        this.article.data.push(...res.data.data.data)
        this.isLoad = res.data.data.meta.total_pages > this.page
      }
    },
    // 加载更多分页
    loadMore() {
      this.page++
      this.fetchData()
    },
  },
}
</script>

<style scoped lang="scss">
/*文章*/
.article-list {
  box-sizing: border-box;
  display: block;
  clear: both;
  padding: 32px 0;
  border-bottom: 1px solid #666;
}

.article-list:hover .article-title {
  color: #f9fafc;
  text-decoration: underline;
  cursor: pointer;
}

.article-item {
  display: flex;
  height: 100%;
  width: 100%;
  text-decoration: none;
  -webkit-transition: background-color 0.35s, color 0.35s, margin 0.45s,
    -webkit-transform 0.5s;
  transition: background-color 0.35s, color 0.35s, margin 0.45s, transform 0.5s;
}

.article-image {
  width: 100px;
  & img {
    width: 100%;
    border-radius: 4px;
  }
}

.article-item-content {
  padding-left: 24px;
  flex: 1;
}

.article-title {
  font-weight: bold;
  font-size: 18px;
  color: #ddd;
  padding: 0;
  margin: 0;
}

.article-description {
  font-size: 14px;
  color: #ddd;
  margin: 12px 0 24px;
}

.article-category {
  font-size: 14px;
  color: #bbb;
}

.empty-data {
  padding: 80px 0;
  text-align: center;
  font-size: 14px;
}

.more {
  cursor: pointer;
  padding: 32px 0;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.more-text {
  font-size: 16px;
  font-weight: 400;
  color: #222222;
  line-height: 22px;
}
.more-arrow {
  width: 16px;
  margin-top: 24px;
}
.more-arrow img {
  width: 100%;
}

@media screen and (max-width: 540px) {
  .article-list {
    padding: 24px 0;
  }

  .article-image {
    width: 90px;
  }
}
</style>

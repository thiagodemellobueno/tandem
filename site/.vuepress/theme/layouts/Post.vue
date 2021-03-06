<template>
  <div
    id="tandem-content_post"
    ref="content"
    class="post-layout content-wrapper-tandem content-wrapper-post"
  >
    <article
      class="post-wrapper"
      itemscope
      itemtype="https://schema.org/BlogPosting"
    >
      <header class="post-theme-content">
        <h1
          class="post-title"
          itemprop="name headline"
        >
          {{ $frontmatter.title }}
        </h1>
        <PostMeta
          :id="$frontmatter.id"
          :name="$frontmatter.author"
          :date="$frontmatter.date"
          :link="$frontmatter.link"
          :location="$frontmatter.location"
          :pic="$frontmatter.pic"
          pic-align="right"
        />
      </header>
      <Content
        class="post-content"
        itemprop="articleBody"
      />
      <hr>
      <footer>
        <div class="custom-block point newsletter">
          <h2>
            Subscribe.<br>
            <small>Get updates for new and similiar content.</small>
          </h2>
          <Newsletter />
        </div>

        <div class="custom-block point tagz">
          <h2>
            Learn more about.
          </h2>
          <div class="post-tags">
            <ul
              v-if="tags"
              class="tags"
            >
              <PostTag
                v-for="tag in tags"
                :key="tag.name"
                class="tag"
                :tag="tag.name"
              />
            </ul>
          </div>
        </div>
      </footer>
      <Toc />
    </article>
  </div>
</template>

<script>
import Newsletter from '@theme/components/Newsletter.vue';
import PostMeta from '@theme/components/PostMeta.vue';
import PostTag from '@theme/components/PostTag.vue';
import Toc from '@theme/components/Toc.vue';
export default {
  components: {Newsletter, PostMeta, PostTag, Toc},
  data() {
    return {
      tags: [],
    };
  },
  mounted() {
    this.tags = [...this.$tags.list].filter(tag => {
      return this.$frontmatter.tags.includes(tag.name);
    });
  },
  jsonld() {
    return {
      '@context': 'https://schema.org',
      '@graph': [
        {
          '@type': 'Article',
          'mainEntityOfPage': {
            '@type': 'WebPage',
            '@id': 'https://thinktandem.io' + this.$page.path,
          },
          'name': this.$title,
          'headline': this.$title,
          'about': [
            this.$page.summary,
          ],
          'image': [
            'https://thinktandem.io' + this.$frontmatter.mainImage,
          ],
          'datePublished': this.$frontmatter.date,
          'dateModified': this.$frontmatter.date,
          'author': {
            '@type': 'Person',
            'name': this.$frontmatter.author,
          },
          'publisher': {
            '@type': 'Organization',
            'name': 'Tandem',
            'logo': {
              '@type': 'ImageObject',
              'url': 'https://thinktandem.io/images/logo.png',
            },
          },
        },
        {
          '@type': 'WebSite',
          '@id': 'https://thinktandem.io',
          'url': 'https://thinktandem.io',
          'name': 'Tandem',
          'publisher': {
            '@id': 'https://thinktandem.io',
          },
        },
        {
          '@type': 'Organization',
          '@id': 'https://thinktandem.io',
          'name': 'Tandem',
          'url': 'https://thinktandem.io',
          'logo': {
            '@type': 'imageObject',
            'url': 'https://thinktandem.io/images/logo.png',
            'caption': 'Tandem Logo',
          },
          'sameAs': [
            'https://twitter.com/thinktandem',
            'https://github.com/thinktandem',
            'https://www.linkedin.com/company/12898991/admin/',
          ],
          'contactPoint': {
            '@type': 'ContactPoint',
            'email': 'sales@thinktandem.io',
            'contactType': 'customer service',
          },
        },
      ],
    };
  },
};
</script>

<style lang="stylus">
.content-wrapper-tandem
  &.content-wrapper-post
    table
      margin-top 2em
      margin-bottom 2em
    .post-content
      margin-top 3.14em
      margin-bottom 3.14em
    .custom-block
      margin-top 2em
      margin-bottom 2em
      &.point
        { cb_point }
    .post-wrapper
      max-width 55rem
.post-theme-content
  { bodyType }
  color $textColor
  position relative
  h1
    font-size typeScale.b
    font-weight 600
    margin-top 0
    margin-bottom .5em
  h2
    font-size typeScale.d
    font-weight 600
  p
    font-weight 300
  footer
    margin-bottom 2em
  .post-title
    padding-top 0
  .post-meta-data
    justify-content space-between

.vuepress-toc
  right 5%
  margin-top 120px
  { displayType }
@media (max-width: $MQMobile)
  .post-theme-content
    padding-top 0
  .post-title
    margin-top 0
@media (max-width: $MQMobileNarrow)
  .post-content
    margin-top 2em
  .post-theme-content
    h1
      font-size typeScale.c
</style>

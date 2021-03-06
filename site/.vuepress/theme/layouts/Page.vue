<template>
  <div class="content-wrapper-tandem content-wrapper-landing-page">
    <SectionHeader
      :title="`${tag}.`"
      :pic="pic"
      :radius="radius"
    >
      <h2>{{ title }}</h2>
      <div>
        <p v-html="byline" />
      </div>
    </SectionHeader>

    <Content />

    <div class="custom-block point recent-work work-grid">
      <h3>
        Recent {{ upperTag }}<br>work.
      </h3>
      <div class="recent-work-wrapper">
        <WorkSummary
          v-for="page in featuredWork"
          :key="page.key"
          :page="page"
          itemprop="blogPost"
          itemscope
          itemtype="https://schema.org/BlogPosting"
        />
        <div
          class="load-more"
          @click="incrementFeaturedWork"
        >
          <button class="btn btn-load-more">
            Next
          </button>
        </div>
      </div>
    </div>

    <div v-if="grids.length > 0">
      <div
        v-for="grid in grids"
        :key="grid.id"
        class="custom-block point grid"
      >
        <h3>
          {{ grid.caption }}
        </h3>
        <ValuesGrid
          :id="grid.id"
          :class="`columns-${grid.columns}`"
          :columns="grid.columns"
          :items="grid.content"
        />
      </div>
    </div>

    <div
      v-if="posts.length > 0"
      class="custom-block point recent-posts"
    >
      <h3>
        Recent {{ upperTag }}<br>content.
      </h3>
      <div class="recent-posts-wrapper">
        <PostSummary
          v-for="page in recentPosts"
          :key="page.key"
          :page="page"
          itemprop="blogPost"
          itemscope
          itemtype="https://schema.org/BlogPosting"
        />
        <div
          v-if="(posts.length - postsSize) >= ((postsIndex * postsSize) + 1)"
          class="load-more"
          @click="incrementPosts"
        >
          <button class="btn btn-load-more">
            Gimme two more
          </button>
        </div>
      </div>
    </div>

    <div class="custom-block point contact-us">
      <h3>
        Let's make some great {{ upperTag }} together.<br>
        <small>Get in touch!</small>
      </h3>
      <ContactForm />
    </div>

    <div class="custom-block point clients">
      <h3>
        You might also want to check out.
      </h3>
      <div class="related-tags">
        <ul
          v-if="relatedTags"
          class="tags"
        >
          <PostTag
            v-for="relatedTag in relatedTags"
            :key="relatedTag.name"
            :link="`/${relatedTag.name}`"
            class="tag"
            :tag="relatedTag.name"
          />
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import ContactForm from '@theme/components/ContactForm';
import PostSummary from '@theme/components/PostSummary.vue';
import PostTag from '@theme/components/PostTag.vue';
import SectionHeader from '@theme/components/SectionHeader';
import ValuesGrid from '@theme/components/ValuesGrid';
import WorkSummary from '@theme/components/WorkSummary.vue';

export default {
  components: {ContactForm, PostSummary, PostTag, SectionHeader, ValuesGrid, WorkSummary},
  data() {
    return {
      featuredWork: null,
      recentPosts: [],
      recentWork: [],
      posts: [],
      postsIndex: 0,
      postsSize: 2,
      workIndex: 0,
    };
  },
  computed: {
    byline() {
      return this.$frontmatter.byline;
    },
    hasPic() {
      return this.$frontmatter.image;
    },
    pic() {
      return (this.hasPic) ? this.$frontmatter.image : null;
    },
    grids() {
      return this.$frontmatter.grids;
    },
    gridExists(index) {
      return !this.grids[index] !== undefined;
    },
    radius() {
      return this.$frontmatter.imageRadius || '0%';
    },
    relatedTags() {
      return this.$frontmatter.relatedTags.map(tag => ({name: tag}));
    },
    tag() {
      return this.$frontmatter.tag;
    },
    title() {
      return this.$frontmatter.title;
    },
    upperTag() {
      return this.tag.charAt(0).toUpperCase() + this.tag.slice(1);
    },
    wins() {
      return this.$frontmatter.wins;
    },
  },
  mounted() {
    // Get all content tagged with the tag
    const content = this.$tags.map[this.tag].pages;
    // Separate content into posts and works
    this.posts = content.filter(page => page.id === 'blog').reverse();
    this.recentWork = content.filter(page => page.id === 'work');
    // Set "page 1" inititially
    this.featuredWork = [this.recentWork[this.workIndex]];
    this.recentPosts = this.posts.slice(0, this.postsSize);
  },
  methods: {
    incrementFeaturedWork() {
      // Increment the featured work
      this.workIndex++;
      // But if we are at the end of the line lets loop back around
      if (this.recentWork.length === this.workIndex) this.workIndex = 0;
      // Set the featured work
      this.featuredWork = [this.recentWork[this.workIndex]];
    },
    incrementPosts() {
      // Increment the posts
      this.postsIndex++;
      // Calculate start and end
      const start = 0 + (this.postsIndex * this.postsSize);
      const end = start + this.postsSize;
      // Return the slice
      this.recentPosts = this.posts.slice(start, end);
    },
  },
};
</script>

<style lang="stylus">
.content-wrapper-tandem
  &.content-wrapper-landing-page
    article
      &.post
        .post-wrapper
          padding 3em
        .post-title
          font-size typeScale.e
        .post-summary
          display none
      &.work
        .work-summary
          padding-bottom 5em
          @media (min-width: $MQMobile)
            padding-bottom 0
    .load-more
      { loadMore }
    .section-header
      h1, h2, p
        color black
      .section-header-right
        text-align center
        @media (min-width: $MQMobile)
          text-align right
    .related-tags
      width 100%
      ul
        display flex
        margin 0
        list-style none
        flex-wrap wrap
        justify-content center
        li
          margin-top 1em
    .custom-block
      p
        { bodyType }
        font-size typeScale.g
        font-weight 300
      &.important
        padding 4em 0
        border-top 1px solid $borderColor
        margin-bottom 0
        @media (min-width: $MQMobile)
          padding 7em 0
          margin-bottom 2em
        &.remote-team
          margin-top 2em
          border-top 1px solid $borderColor
          border-bottom 0
      &.point
        { cb_point }
        p
          width 100%
          &.custom-block-title
            width 100%
            @media (min-width: $MQMobile)
              width 35%
        &.recent-work
          .recent-work-wrapper
            width 100%
        &.recent-posts
          .recent-posts-wrapper
            width 100%
        &.grid
          .columns-1
            .values-item
              padding 1em 2em
              &:before
                all unset
              @media (max-width: $MQMobile)
                width 100%
</style>

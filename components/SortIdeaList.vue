<template>
  <div class="sortingWrapper">
    <a-cascader
      :options="options"
      expand-trigger="hover"
      placeholder="Sort By"
      @change="onChange"
    />
  </div>
</template>

<script>
export default {
  name: "SortIdeasList",
  data() {
    return {
      options: [
        {
          value: "title",
          label: "Title",
          children: [
            {
              value: "asc",
              label: "Ascending"
            },
            {
              value: "desc",
              label: "Descending"
            }
          ]
        },
        {
          value: "created_date",
          label: "Created Date",
          children: [
            {
              value: "asc",
              label: "Ascending"
            },
            {
              value: "desc",
              label: "Descending"
            }
          ]
        }
      ]
    };
  },
  methods: {
    onChange(value) {
      this.$emit("sort", value);
    }
  }
};
</script>

<style></style>

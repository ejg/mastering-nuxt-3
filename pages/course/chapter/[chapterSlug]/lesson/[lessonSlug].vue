<template>
  <div>
    <p class="mt-0 uppercase font-bold text-slate-400 mb-1">
      Lesson {{ chapter.number }} - {{ lesson.number }}
    </p>
    <h2 class="my-0">{{ lesson.title }}</h2>
    <div class="flex space-x-4 mt-2 mb-8">
      <NuxtLink
        v-if="lesson.sourceUrl"
        class="font-normal text-md text-gray-500"
        :to="lesson.sourceUrl"
      >
        Download Source Code
      </NuxtLink>
      <NuxtLink
        v-if="lesson.downloadUrl"
        class="font-normal text-md text-gray-500"
        :to="lesson.downloadUrl"
      >
        Download Video
      </NuxtLink>
    </div>
    <VideoPlayer v-if="lesson.videoId" :videoId="lesson.videoId" />
    <p>{{ lesson.text }}</p>
    <LessonCompleteButton
      :model-value="isLessonComplete"
      @update:model-value="toggleComplete"
    />
  </div>
</template>

<script setup>
const course = useCourse();
const route = useRoute();

const chapter = computed(() => {
  return course.chapters.find(
    (chapter) => chapter.slug === route.params.chapterSlug
  );
});
const lesson = computed(() => {
  return chapter.value.lessons.find(
    (lesson) => lesson.slug === route.params.lessonSlug
  );
});
const title = computed(() => {
  return `${lesson.value.title} - ${course.title}`;
});
const chapterNumber = computed(() => {
  return chapter.value.number - 1;
});
const lessonNumber = computed(() => {
  return lesson.value.number - 1;
});

useHead({ title: title });
const progress = useLocalStorage('progress', () => {
  return [];
});
/*const progress = useState('progress', () => {
  return [];
}); */
const isLessonComplete = computed(() => {
  if (!progress.value[chapterNumber.value]) {
    return false;
  }
  return !!progress.value[chapterNumber.value][lessonNumber.value];
});

const toggleComplete = () => {
  if (!progress.value[chapterNumber.value]) {
    progress.value[chapterNumber.value] = [];
  }
  progress.value[chapterNumber.value][lessonNumber.value] =
    !isLessonComplete.value;
};
</script>
<style scoped></style>

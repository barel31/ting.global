<template>
  <Page name="home" :showHeading="false" :mainSection="false">
    <PageHeadingNET />
    <Intro />
    <main class="home">
      <TopChallenges />
      <!-- <LatestChallenges /> -->
      <TryItYourself />
      <Testimonials />
      <LatestNews />
    <Partners class="section-white" />
    </main>
  </Page>
</template>

<script>
import Intro from "~/components/home/Intro";
import TopChallenges from "~/components/home/TopChallenges";
import LatestChallenges from "~/components/home/LatestChallenges";
import TryItYourself from "~/components/home/TryItYourself";
import Testimonials from "~/components/home/Testimonials";
import LatestNews from "~/components/home/LatestNews";
import Partners from "~/components/about/Partners";

export default {
  components: {
    Intro,
    TopChallenges,
    LatestChallenges,
    TryItYourself,
    Testimonials,
    LatestNews,
    Partners
  }
};
</script>

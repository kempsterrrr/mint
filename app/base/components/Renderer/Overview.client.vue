<template>
  <section class="renderers" id="installed-renderers">
    <h1>{{ $t('renderer.installed') }}</h1>

    <div>
      <RendererOverviewCard
        v-for="renderer of installedRenderers"
        :renderer="renderer"
      />
    </div>
  </section>

  <section class="renderers" id="available-renderers">
    <h1>{{ $t('renderer.available') }}</h1>

    <div v-if="availableRenderers.length">
      <RendererOverviewCard
        v-for="renderer of availableRenderers"
        :renderer="renderer"
      >
        <template #after>
          <div class="actions">
            <RendererInstallButton
              :collection="collection"
              :renderer="renderer"
            />
          </div>
        </template>
      </RendererOverviewCard>
    </div>

    <div v-if="! availableRenderers.length" class="empty">
      <p>{{ $t('renderer.all_installed') }}</p>
    </div>

    <RendererInstallCustom :collection="collection" />
  </section>
</template>

<script setup>
const props = defineProps(['collection'])

const appConfig = useAppConfig()
const store = useOnchainStore()
const mainChainId = useMainChainId()

const installedRenderers = computed(() => props.collection.renderers)

const availableRenderers = computed(
  () => appConfig.knownRenderers[mainChainId].filter(r =>
    !installedRenderers.value.map(cr => cr.address).includes(r.address) &&
    !r.deprecated
  )
)

onMounted(() => {
  store.fetchCollectionRenderers(props.collection.address)
})
</script>

<style scoped>
.renderers {
  display: grid;
  gap: var(--spacer);
  overflow-x: hidden;

  > h1 {
    font-size: var(--font-lg);
    border-bottom: var(--border);
    padding-bottom: var(--size-2);
  }

  .empty {
    color: var(--muted);
  }
}
</style>

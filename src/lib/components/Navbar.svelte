<script lang="ts" module>
  import { logEvent } from '$lib/util/stats';
  import { version } from 'mermaid/package.json';

  void logEvent('version', {
    mermaidVersion: version
  });
</script>

<script lang="ts">
  import MainMenu from '$/components/MainMenu.svelte';
  import McWrapper from '$/components/McWrapper.svelte';
  import { Separator } from '$/components/ui/separator';
  import { Switch } from '$/components/ui/switch';
  import { urlsStore } from '$lib/util/state';
  import { MCBaseURL } from '$lib/util/util';
  import type { Snippet } from 'svelte';
  import MermaidIcon from '~icons/custom/mermaid';

  interface Props {
    mobileToggle?: Snippet;
    children: Snippet;
  }

  let { children, mobileToggle }: Props = $props();

  const isReferral = document.referrer.includes(MCBaseURL);
</script>

<nav class="z-50 flex p-4 sm:p-6">
  <div class="flex flex-1 items-center gap-2">
    <MainMenu />
    <MermaidIcon class="size-6" />
    <div
      class="flex items-center justify-center gap-4 font-medium"
      class:flex-row-reverse={isReferral}
      id="switcher">
      <McWrapper>
        <div class="hidden items-center justify-center gap-4 md:flex">
          <Separator orientation="vertical" />
          <Switch
            checked={isReferral}
            class="data-[state=checked]:bg-secondary"
            id="editorMode"
            onclick={() => {
              logEvent('playgroundToggle', { isReferred: isReferral });
              // Wait for the event to be logged
              setTimeout(() => {
                window.open(
                  $urlsStore.mermaidChart({ medium: 'toggle' }).playground,
                  '_self',
                  // Do not send referrer header, if the user already came from playground
                  isReferral ? 'noreferrer' : ''
                );
              }, 100);
            }} />

          <a
            class="whitespace-nowrap"
            href={$urlsStore.mermaidChart({ medium: 'toggle' }).playground}>
            Playground <span class="hidden text-sm opacity-50 lg:inline"
              >- more features, no account required</span>
          </a>
        </div>
      </McWrapper>
    </div>
  </div>
  <div
    class="hidden flex-nowrap items-center justify-between gap-3 overflow-hidden md:flex"
    id="menu">
    <Separator orientation="vertical" />
    {@render children()}
  </div>
  {@render mobileToggle?.()}
</nav>

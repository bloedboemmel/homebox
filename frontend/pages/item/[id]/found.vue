<script setup lang="ts">
  import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
  import { Button } from "@/components/ui/button";
  import MdiPackageVariant from "~icons/mdi/package-variant";
  import MdiLogin from "~icons/mdi/login";
  import MdiEmail from "~icons/mdi/email";

  definePageMeta({
    layout: "empty",
  });

  const route = useRoute();
  const api = usePublicApi();
  
  const itemId = computed<string>(() => route.params.id as string);

  const { data: itemInfo, error } = await useAsyncData(`item-${itemId.value}`, async () => {
    const { data, error } = await api.getPublicItemInfo(itemId.value);
    if (error) {
      throw createError({
        statusCode: 404,
        statusMessage: "Item not found"
      });
    }
    return data;
  });

  const loginUrl = computed(() => {
    return `/?redirect=${encodeURIComponent(route.fullPath)}`;
  });
</script>

<template>
  <div class="flex min-h-screen flex-col bg-gradient-to-br from-primary/20 to-primary/10">
    <div class="flex-1 flex items-center justify-center p-6">
      <Card class="w-full max-w-lg shadow-lg">
        <CardHeader class="text-center">
          <div class="mx-auto mb-4 flex h-20 w-20 items-center justify-center rounded-full bg-primary/10">
            <MdiPackageVariant class="h-10 w-10 text-primary" />
          </div>
          <CardTitle class="text-2xl font-bold text-primary">
            Nice you found my {{ itemInfo?.name || 'item' }}!
          </CardTitle>
        </CardHeader>
        <CardContent class="space-y-6 text-center">
          <div v-if="itemInfo?.email" class="flex items-center justify-center gap-2 rounded-lg bg-muted p-4">
            <MdiEmail class="h-5 w-5 text-muted-foreground" />
            <p class="text-sm text-muted-foreground">
              Please send me an email at 
              <a 
                :href="`mailto:${itemInfo.email}?subject=Found your ${itemInfo.name}`" 
                class="font-medium text-primary hover:underline"
              >
                {{ itemInfo.email }}
              </a>
            </p>
          </div>
          
          <div v-else class="rounded-lg bg-muted p-4">
            <p class="text-sm text-muted-foreground">
              Please contact the owner to return this item.
            </p>
          </div>

          <div class="pt-4">
            <Button as-child class="w-full">
              <NuxtLink :to="loginUrl">
                <MdiLogin class="mr-2 h-4 w-4" />
                Login to HomeBox
              </NuxtLink>
            </Button>
          </div>
        </CardContent>
      </Card>
    </div>
    
    <footer class="p-4 text-center">
      <p class="text-sm text-muted-foreground">
        Powered by 
        <a href="https://homebox.software" target="_blank" class="hover:underline">
          HomeBox
        </a>
      </p>
    </footer>
  </div>
</template>
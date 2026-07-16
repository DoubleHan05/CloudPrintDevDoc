---
layout: page
---

<script setup>
import { useRouter } from 'vitepress'
const router = useRouter()
if (typeof window !== 'undefined') {
  router.go('/zh/quick/overview')
}
</script>

<template>
    <section>
        <div class="table-row" v-if="folders.length > 0">
            <div class="table-cell text-left title">FOLDERS</div>
            <div class="table-cell"></div>
            <div class="table-cell">Size</div>
        </div>
        <div class="table-row" v-for="(folder, folderId) in folders" :key="folderId">
            <div class="table-cell text-left">{{ folder.path }}</div>
            <div class="table-cell"></div>
            <div class="table-cell" :class="getDecoration(folder)">
                {{ $filters.bytes(folder.size) }}
            </div>
        </div>
    </section>
</template>

<script>
export default {
    props: {
        data: {
            type: Object
        }
    },
    computed: {
        stats() {
            return this.data.stats['folders'];
        },
        folders() {
            return this.stats.map((folderData) => {
                return {
                    path: folderData['path'],
                    size: folderData['size'],
                    careful: folderData['careful'],
                    warning: folderData['warning'],
                    critical: folderData['critical']
                };
            });
        }
    },
    methods: {
        getDecoration(folder) {
            if (!Number.isInteger(folder.size)) {
                return;
            }
            if (folder.critical !== null && folder.size > folder.critical * 1000000) {
                return 'critical';
            } else if (folder.warning !== null && folder.size > folder.warning * 1000000) {
                return 'warning';
            } else if (folder.careful !== null && folder.size > folder.careful * 1000000) {
                return 'careful';
            }
            return 'ok';
        }
    }
};
</script>
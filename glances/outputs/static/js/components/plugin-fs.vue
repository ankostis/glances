<template>
    <section>
        <div class="table-row">
            <div class="table-cell text-left title">FILE SYS</div>
            <div class="table-cell">
                <span v-show="!args.fs_free_space">Used</span>
                <span v-show="args.fs_free_space">Free</span>
            </div>
            <div class="table-cell">Total</div>
        </div>
        <div class="table-row" v-for="(fs, fsId) in fileSystems" :key="fsId">
            <div class="table-cell text-left">
                {{ fs.shortMountPoint }}
                <span v-if="fs.shortMountPoint.length <= 12" class="visible-lg-inline">
                    ({{ fs.name }})
                </span>
            </div>
            <div class="table-cell" :class="getDecoration(fs.mountPoint, 'used')">
                <span v-show="!args.fs_free_space">
                    {{ $filters.bytes(fs.used) }}
                </span>
                <span v-show="args.fs_free_space">
                    {{ $filters.bytes(fs.free) }}
                </span>
            </div>
            <div class="table-cell">{{ $filters.bytes(fs.size) }}</div>
        </div>
    </section>
</template>

<script>
import { orderBy } from 'lodash';
import { store } from '../store.js';

export default {
    props: {
        data: {
            type: Object
        }
    },
    data() {
        return {
            store
        };
    },
    computed: {
        args() {
            return this.store.args || {};
        },
        stats() {
            return this.data.stats['fs'];
        },
        view() {
            return this.data.views['fs'];
        },
        fileSystems() {
            const fileSystems = this.stats.map((fsData) => {
                let shortMountPoint = fsData['mnt_point'];
                if (shortMountPoint.length > 22) {
                    shortMountPoint = '_' + fsData['mnt_point'].slice(-21);
                }
                return {
                    name: fsData['device_name'],
                    mountPoint: fsData['mnt_point'],
                    shortMountPoint: shortMountPoint,
                    percent: fsData['percent'],
                    size: fsData['size'],
                    used: fsData['used'],
                    free: fsData['free']
                };
            });
            return orderBy(fileSystems, ['mnt_point']);
        }
    },
    methods: {
        getDecoration(mountPoint, field) {
            if (this.view[mountPoint][field] == undefined) {
                return;
            }
            return this.view[mountPoint][field].decoration.toLowerCase();
        }
    }
};
</script>
---
title: "Mozilla Hubs: シーンのパラメータを変える"
emoji: "🦝"
type: "tech"
topics: [MozillaHubs, Blender, VR]
published: true
created: "2022-03-05"
---

hubsの部屋にアクセスする時にパラメータを渡せる。
パラメータは、下記の様な感じで Query Stringを使って渡す。

```
https://hubs.mozilla.com/FooBarBaz/foo-bar-baz?thirdPerson=1
```

部屋のURLに指定できるオプションの公式リスト。

https://hubs.mozilla.com/docs/hubs-query-string-parameters.html

これ以外にもソースコードを見ると色々ある。

ざっと眺めるとqsで始まる関数を使ってQuery Stringを拾っている感じ。

例えば、

```
./src/hub.js:const isTelemetryDisabled = qsTruthy("disable_telemetry");
./src/storage/store.js:  const qsDefault = qsGet("default_material_quality");
./src/scene-entry-manager.js:    const avatarScale = parseInt(qs.get("avatar_scale"), 10);
```

- *qsTruthy*は、1か0を指定する。
- *qsGet*は、任意の文字列を指定する。
- *parseInt*は、整数を指定する。

こういうオプションは1つのソースコードで一括管理したくなるけど、あちこちに散らばっている。
管理されてるのかな？

以下は githubのsrcの下から抜き出してみた。(2022年3月時点)

| name | type | file | line# | code |
|------|------|------|-------|------|
| allow_multi | bool | react-components/ui-root.js | 280 |     if (qsTruthy("allow_multi |
| audio_volume | str | scene-entry-manager.js | 549 |       let audioVolume = Number(qs.get("audio_volume |
| auth_origin | str | react-components/auth/VerifyModalContainer.js | 54 |     const origin = qs.get("auth_origin |
| auth_payload | str | react-components/auth/VerifyModalContainer.js | 26 |             payload: qs.get("auth_payload |
| auth_token | str | react-components/auth/VerifyModalContainer.js | 24 |             token: qs.get("auth_token |
| auth_topic | str | react-components/auth/VerifyModalContainer.js | 23 |             topic: qs.get("auth_topic |
| avatarEditorDebug | bool | react-components/ui-root.js | 98 | const avatarEditorDebug = qsTruthy("avatarEditorDebug |
| avatar_id | str | avatar.js | 149 |   const avatarId = qs.get("avatar_id |
| avatar_scale | int | scene-entry-manager.js | 165 |     const avatarScale = parseInt(qs.get("avatar_scale |
| batchImages | bool | components/media-loader.js | 41 | const forceImageBatching = qsTruthy("batchImages |
| batchMeshes | bool | components/media-loader.js | 40 | const forceMeshBatching = qsTruthy("batchMeshes |
| bot | bool | scene-entry-manager.js | 7 | const isBotMode = qsTruthy("bot |
| debug | bool | systems/personal-space-bubble.js | 7 | const isDebug = qsTruthy("debug |
| debugLocalScene | bool | scene-entry-manager.js | 295 |       if (qsTruthy("debugLocalScene |
| debugNavmesh | bool | systems/nav.js | 30 |     if (qsTruthy("debugNavmesh |
| debug_log | bool | utils/debug-log.js | 3 | const showLog = qsTruthy("debug_log |
| default_material_quality | str | storage/store.js | 29 |   const qsDefault = qsGet("default_material_quality |
| default_mobile_material_quality | str | storage/store.js | 22 |     const qsMobileDefault = qsGet("default_mobile_material_quality |
| disableBatching | bool | components/media-loader.js | 42 | const disableBatching = qsTruthy("disableBatching |
| disable_telemetry | bool | hub.js | 275 | const isTelemetryDisabled = qsTruthy("disable_telemetry |
| dui | bool | systems/userinput/resolve-action-sets.js | 5 | const debugUserInput = qsTruthy("dui |
| embed_token | str | hub.js | 217 | if (isEmbed && !qs.get("embed_token |
| envSettingsDebug | bool | systems/environment-system.js | 51 |     if (qsTruthy("envSettingsDebug |
| force_enable_touchscreen | bool | scene-entry-manager.js | 80 |     if (isMobile || forceEnableTouchscreen || qsTruthy("force_enable_touchscreen |
| force_tcp | str | hub.js | 631 |         forceTcp: qs.get("force_tcp |
| force_turn | str | hub.js | 633 |         iceTransportPolicy: qs.get("force_tcp") || qs.get("force_turn |
| fov | str | systems/camera-system.js | 8 | const customFOV = qsGet("fov |
| hlsDebug | bool | components/media-video.js | 612 |               debug: qsTruthy("hlsDebug |
| hub_id | str | utils/hub-utils.js | 7 |     qs.get("hub_id |
| hub_invite_id | str | hub.js | 1081 |       hubInviteId: qs.get("hub_invite_id |
| idle_timeout | int | systems/idle-detector.js | 3 | const IDLE_TIMEOUT_MS = (parseInt(qs.get("idle_timeout |
| izs | str | systems/userinput/bindings/keyboard-mouse-user.js | 23 | const inspectZoomSpeed = parseFloat(qs.get("izs |
| log_filter | str | utils/logging.js | 8 | const logFilter = qs.get("log_filter |
| offline | bool | scene-entry-manager.js | 89 |     if (qsTruthy("offline |
| phx_host | str | utils/phoenix-utils.js | 100 |   let host = qs.get("phx_host |
| phx_port | str | utils/phoenix-utils.js | 104 |     qs.get("phx_port |
| phx_protocol | str | utils/phoenix-utils.js | 131 |       qs.get("phx_protocol |
| required_ret_pool | str | hub.js | 971 |       (qs.get("required_ret_version") !== reticulumMeta.version || qs.get("required_ret_pool |
| required_ret_version | str | hub.js | 970 |       qs.get("required_ret_version |
| required_version | str | hub.js | 958 |     if (qs.get("required_version |
| scene_id | str | scene.js | 71 |   const sceneId = qs.get("scene_id |
| sign_in_destination_url | str | react-components/auth/SignInModalContainer.js | 57 |   const redirectUrl = qs.get("sign_in_destination_url |
| sign_in_reason | str | react-components/auth/SignInModalContainer.js | 74 |           signInReason={qs.get("sign_in_reason |
| thirdPerson | bool | systems/camera-system.js | 9 | const enableThirdPersonMode = qsTruthy("thirdPerson |
| userinput_debug | bool | systems/userinput/userinput-debug.js | 63 |       if (qsTruthy("userinput_debug |
| video_capture | bool | systems/capture-system.js | 34 |     return qsTruthy("video_capture |
| vr_entry_type | str | hub.js | 324 | const qsVREntryType = qs.get("vr_entry_type |
| vrstats | bool | components/stats-plus.js | 80 |     this.vrStatsEnabled = qsTruthy("vrstats |
| waypointLerp | bool | systems/character-controller-system.js | 17 | const qsAllowWaypointLerp = qsTruthy("waypointLerp |

コードは[こちら](https://gist.github.com/tanupoo/d9b8d177b3e7f4597c2e35a09a59c10c)。
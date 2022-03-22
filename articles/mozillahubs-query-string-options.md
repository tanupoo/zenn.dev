---
title: "Mozilla Hubs: ストリーマーモードを使ってみる"
emoji: ""
type: "tech"
topics: [MozillaHubs, Blender, VR]
published: false
---

created: 2022-03-05

以下のように、部屋のURLにQuery Stringをつけることでオプションを指定できる。

```
https://hubs.mozilla.com/FooBarBaz/foo-bar-baz?thirdPerson=1
```

部屋のURLに指定できるオプションのリスト。

https://hubs.mozilla.com/docs/hubs-query-string-parameters.html

これ以外にもソースコードを見ると色々ある。

ざっと眺めるとqsで始まる関数を使ってQuery Stringを拾っている感じ。

例えば、
```
./src/hub.js:const isTelemetryDisabled = qsTruthy("disable_telemetry");
./src/storage/store.js:  const qsDefault = qsGet("default_material_quality");
./src/scene-entry-manager.js:    const avatarScale = parseInt(qs.get("avatar_scale"), 10);
```

こういうオプションは普通は1つのソースコードで一括管理したくなるけど、あちこちに散らばっている。
管理されてるのかな？

以下は 2022年3月時点でgithubにあるもの。

```
% find . -name '*.js' | xargs grep -E '(qs.get|qsTruthy|qsGet)'
./admin/src/utils/idle-detector.js:const IDLE_TIMEOUT_MS = (parseInt(qs.get("idle_timeout"), 10) || 1 * 60 * 60) * 1000;
./admin/src/react-components/service-editor.js:      .filter(([, descriptor]) => qs.get("show_internal_configs") !== null || descriptor.internal !== "true")
./admin/src/react-components/service-editor.js:      .filter(([, descriptor]) => qs.get("show_deprecated_configs") !== null || descriptor.deprecated !== "true")
./admin/src/admin.js:    if (process.env.NODE_ENV !== "development" || qs.get("idle_timeout")) detectIdle();
./src/avatar.js:  const avatarId = qs.get("avatar_id") || document.location.pathname.substring(1).split("/")[1];
./src/hub.js:if (isEmbed && !qs.get("embed_token")) {
./src/hub.js:const isBotMode = qsTruthy("bot");
./src/hub.js:const isTelemetryDisabled = qsTruthy("disable_telemetry");
./src/hub.js:const isDebug = qsTruthy("debug");
./src/hub.js:const qsVREntryType = qs.get("vr_entry_type");
./src/hub.js:    qsTruthy("allow_idle") || (process.env.NODE_ENV === "development" && !qs.get("idle_timeout"));
./src/hub.js:  if (qsTruthy("debugLocalScene") && sceneUrl?.startsWith("blob:")) {
./src/hub.js:        forceTcp: qs.get("force_tcp"),
./src/hub.js:        forceTurn: qs.get("force_turn"),
./src/hub.js:        iceTransportPolicy: qs.get("force_tcp") || qs.get("force_turn") ? "relay" : "all"
./src/hub.js:  if (qs.get("required_version") && process.env.BUILD_VERSION) {
./src/hub.js:    if (qs.get("required_version") !== buildNumber) {
./src/hub.js:      qs.get("required_ret_version") &&
./src/hub.js:      (qs.get("required_ret_version") !== reticulumMeta.version || qs.get("required_ret_pool") !== reticulumMeta.pool)
./src/hub.js:      hubInviteId: qs.get("hub_invite_id"),
./src/scene.js:  const sceneId = qs.get("scene_id") || document.location.pathname.substring(1).split("/")[1];
./src/change-hub.js:  const newHubId = qs.get("hub_id") || document.location.pathname.substring(1).split("/")[0];
./src/utils/logging.js:const isDebug = qsTruthy("debug");
./src/utils/logging.js:const logFilter = qs.get("log_filter") || (isDebug && "naf-janus-adapter:*,naf-dialog-adapter:*,mediasoup*");
./src/utils/hub-utils.js:    qs.get("hub_id") ||
./src/utils/debug-log.js:const showLog = qsTruthy("debug_log");
./src/utils/phoenix-utils.js:  const phxHostOverride = qs.get("phx_host");
./src/utils/phoenix-utils.js:  let host = qs.get("phx_host");
./src/utils/phoenix-utils.js:    qs.get("phx_port") ||
./src/utils/phoenix-utils.js:      qs.get("phx_protocol") ||
./src/utils/qs_truthy.js:export default function qsTruthy(param) {
./src/utils/qs_truthy.js:  const val = qs.get(param);
./src/utils/qs_truthy.js:export function qsGet(param) {
./src/utils/qs_truthy.js:  return qs.get(param);
./src/storage/store.js:    const qsMobileDefault = qsGet("default_mobile_material_quality");
./src/storage/store.js:  const qsDefault = qsGet("default_material_quality");
./src/components/media-loader.js:const forceMeshBatching = qsTruthy("batchMeshes");
./src/components/media-loader.js:const forceImageBatching = qsTruthy("batchImages");
./src/components/media-loader.js:const disableBatching = qsTruthy("disableBatching");
./src/components/stats-plus.js:    this.vrStatsEnabled = qsTruthy("vrstats");
./src/components/media-video.js:              debug: qsTruthy("hlsDebug"),
./src/scene-entry-manager.js:const isBotMode = qsTruthy("bot");
./src/scene-entry-manager.js:const isDebug = qsTruthy("debug");
./src/scene-entry-manager.js:    if (isMobile || forceEnableTouchscreen || qsTruthy("force_enable_touchscreen")) {
./src/scene-entry-manager.js:    if (qsTruthy("offline")) return;
./src/scene-entry-manager.js:    const avatarScale = parseInt(qs.get("avatar_scale"), 10);
./src/scene-entry-manager.js:      if (qsTruthy("debugLocalScene")) {
./src/scene-entry-manager.js:      let audioVolume = Number(qs.get("audio_volume") || "1.0");
./src/react-components/auth/VerifyModalContainer.js:            topic: qs.get("auth_topic"),
./src/react-components/auth/VerifyModalContainer.js:            token: qs.get("auth_token"),
./src/react-components/auth/VerifyModalContainer.js:            origin: qs.get("auth_origin"),
./src/react-components/auth/VerifyModalContainer.js:            payload: qs.get("auth_payload")
./src/react-components/auth/VerifyModalContainer.js:    const origin = qs.get("auth_origin");
./src/react-components/auth/SignInModalContainer.js:  const redirectUrl = qs.get("sign_in_destination_url") || "/";
./src/react-components/auth/SignInModalContainer.js:          signInReason={qs.get("sign_in_reason")}
./src/react-components/ui-root.js:const avatarEditorDebug = qsTruthy("avatarEditorDebug");
./src/react-components/ui-root.js:    if (qsTruthy("allow_multi") || this.props.store.state.preferences["allowMultipleHubsInstances"]) return;
./src/systems/camera-system.js:const customFOV = qsGet("fov");
./src/systems/camera-system.js:const enableThirdPersonMode = qsTruthy("thirdPerson");
./src/systems/character-controller-system.js:const qsAllowWaypointLerp = qsTruthy("waypointLerp");
./src/systems/media-frames.js:const DEBUG = qsTruthy("debug");
./src/systems/idle-detector.js:const IDLE_TIMEOUT_MS = (parseInt(qs.get("idle_timeout"), 10) || 2 * 60 * 60) * 1000;
./src/systems/userinput/userinput-debug.js:      if (qsTruthy("userinput_debug") && !this.userinputFrameStatus) {
./src/systems/userinput/resolve-action-sets.js:const debugUserInput = qsTruthy("dui");
./src/systems/userinput/bindings/keyboard-mouse-user.js:const inspectZoomSpeed = parseFloat(qs.get("izs")) || -10.0;
./src/systems/capture-system.js:    return qsTruthy("video_capture") && window.MediaRecorder && MediaRecorder.isTypeSupported("video/webm; codecs=vp8");
./src/systems/personal-space-bubble.js:const isDebug = qsTruthy("debug");
./src/systems/environment-system.js:    if (qsTruthy("envSettingsDebug")) {
./src/systems/nav.js:    if (qsTruthy("debugNavmesh")) {
```

オプションだけ抜き出してみた。(2022年3月時点)

```
allow_idle
allow_multi
audio_volume
auth_origin
auth_payload
auth_token
auth_topic
avatarEditorDebug
avatar_id
avatar_scale
batchImages
batchMeshes
bot
debug
debugLocalScene
debugNavmesh
debug_log
default_material_quality
default_mobile_material_quality
disableBatching
disable_telemetry
dui
embed_token
envSettingsDebug
force_enable_touchscreen
force_tcp
force_turn
fov
hlsDebug
hub_id
hub_invite_id
idle_timeout
izs
log_filter
offline
phx_host
phx_port
phx_protocol
required_ret_version
required_version
scene_id
show_deprecated_configs
show_internal_configs
sign_in_destination_url
sign_in_reason
thirdPerson
userinput_debug
video_capture
vr_entry_type
vrstats
waypointLerp
```






mozilla hubs: Query String Options

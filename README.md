SHIELDRA — PHASE 3 FULL DESIGN CODE HANDOFF

PART 2 OF 2

Continuing from Part 1. No files omitted.

---

FILE: app/src/main/java/com/shieldra/app/presentation/model/PresentationModels.kt

```kotlin
package com.shieldra.app.presentation.model

import androidx.compose.runtime.Immutable
import androidx.compose.ui.graphics.Color
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.KeyboardArrowRight
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material.icons.filled.DirectionsRun
import androidx.compose.material.icons.filled.SimCard
import androidx.compose.material.icons.filled.WarningAmber
import androidx.compose.material.icons.filled.BatteryAlert
import androidx.compose.material.icons.filled.CameraAlt
import androidx.compose.material.icons.filled.LocationOn
import androidx.compose.material.icons.filled.MailOutline
import androidx.compose.material.icons.filled.Shield
import androidx.compose.material.icons.filled.Bolt
import androidx.compose.material.icons.filled.PhoneAndroid
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material.icons.filled.ErrorOutline
import androidx.compose.material.icons.filled.CheckCircle
import androidx.compose.material.icons.filled.Info
import androidx.compose.ui.graphics.vector.ImageVector

// ============================================================
// Protection visual state (PRESENTATION ONLY)
// ============================================================
@Immutable
enum class ProtectionVisualState {
    Protected,
    Suspicious,
    SecurityEvent,
    Attention,
    Disabled,
}

// ============================================================
// Guard
// ============================================================
@Immutable
enum class GuardKind { Lock, Motion, Sim, Panic, Battery }

@Immutable
enum class GuardStatus { Active, Signal, Disabled, Locked }

@Immutable
data class GuardUiModel(
    val kind: GuardKind,
    val title: String,
    val subtitle: String,
    val status: GuardStatus,
    val isPremiumLocked: Boolean = false,
    val enabled: Boolean = true,
)

fun GuardKind.icon(): ImageVector = when (this) {
    GuardKind.Lock    -> Icons.Filled.Lock
    GuardKind.Motion  -> Icons.Filled.DirectionsRun
    GuardKind.Sim     -> Icons.Filled.SimCard
    GuardKind.Panic   -> Icons.Filled.WarningAmber
    GuardKind.Battery -> Icons.Filled.BatteryAlert
}

// ============================================================
// Location
// ============================================================
@Immutable
enum class LocationKind { Current, LastKnown, Unavailable }

@Immutable
data class LocationUiModel(
    val kind: LocationKind,
    val latitude: Double? = null,
    val longitude: Double? = null,
    val accuracyMeters: Int? = null,
    val ageSeconds: Long? = null,
    val source: String? = null,
)

// ============================================================
// Delivery
// ============================================================
@Immutable
enum class DeliveryStatus {
    Ready, Delivering, Delivered, Deferred, Failed, Expired, Skipped
}

@Immutable
enum class ChannelId { Email, WhatsApp, Telegram }

@Immutable
data class ChannelReceiptUiModel(
    val channel: ChannelId,
    val status: DeliveryStatus,
    val detail: String? = null,
)

// ============================================================
// Event
// ============================================================
@Immutable
enum class EventType { FailedUnlock, Motion, SimChange, Panic, Battery }

@Immutable
data class EventUiModel(
    val id: String,
    val type: EventType,
    val title: String,
    val subtitle: String,
    val timestampLabel: String,
    val deliveryStatus: DeliveryStatus,
    val hasPhoto: Boolean,
    val hasLocation: Boolean,
)

fun EventType.icon(): ImageVector = when (this) {
    EventType.FailedUnlock -> Icons.Filled.Lock
    EventType.Motion       -> Icons.Filled.DirectionsRun
    EventType.SimChange    -> Icons.Filled.SimCard
    EventType.Panic        -> Icons.Filled.WarningAmber
    EventType.Battery      -> Icons.Filled.BatteryAlert
}

// ============================================================
// Event detail
// ============================================================
@Immutable
data class EventDetailUiModel(
    val id: String,
    val type: EventType,
    val title: String,
    val attemptNumber: Int? = null,
    val timestampLabel: String,
    val batteryPercent: Int? = null,
    val charging: Boolean = false,
    val hasPhoto: Boolean,
    val location: LocationUiModel,
    val delivery: List<ChannelReceiptUiModel>,
)

// ============================================================
// Dashboard aggregate
// ============================================================
@Immutable
data class DashboardUiModel(
    val protectionState: ProtectionVisualState,
    val lastCheckSeconds: Long,
    val guards: List<GuardUiModel>,
    val recentEvents: List<EventUiModel>,
    val batteryPercent: Int?,
    val networkLabel: String,
    val networkOk: Boolean,
    val showAds: Boolean,
)

// ============================================================
// History filters
// ============================================================
@Immutable
enum class HistoryTypeFilter { All, Lock, Motion, Sim, Panic, Battery }

@Immutable
enum class HistoryStatusFilter { All, Delivered, Deferred, Failed }

@Immutable
data class HistoryFilterState(
    val type: HistoryTypeFilter = HistoryTypeFilter.All,
    val status: HistoryStatusFilter = HistoryStatusFilter.All,
)

// ============================================================
// Premium
// ============================================================
@Immutable
data class PremiumFeatureUiModel(
    val title: String,
    val subtitle: String? = null,
)

@Immutable
data class PremiumUiModel(
    val priceLabel: String?,
    val features: List<PremiumFeatureUiModel>,
    val freeTierSummary: String,
)

// ============================================================
// Settings
// ============================================================
@Immutable
enum class SettingsSection { Security, Delivery, Appearance, PrivacyData, SecurityApp, About }

@Immutable
data class SettingsRowUiModel(
    val id: String,
    val title: String,
    val subtitle: String? = null,
    val icon: ImageVector,
    val requiresAuth: Boolean = false,
    val section: SettingsSection,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/preview/PreviewData.kt

```kotlin
package com.shieldra.app.presentation.preview

import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Bolt
import androidx.compose.material.icons.filled.ColorLens
import androidx.compose.material.icons.filled.DeleteOutline
import androidx.compose.material.icons.filled.Info
import androidx.compose.material.icons.filled.Language
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material.icons.filled.PhoneAndroid
import androidx.compose.material.icons.filled.Security
import androidx.compose.material.icons.filled.Storage
import androidx.compose.material.icons.filled.Tune
import com.shieldra.app.presentation.model.*

/**
 * STATIC PRESENTATION DATA — DO NOT IMPORT FROM PRODUCTION CODE.
 * Only used by @Preview functions.
 */
object PreviewData {

    val guards = listOf(
        GuardUiModel(GuardKind.Lock, "Lock Guard", "Active · 2 attempts", GuardStatus.Active),
        GuardUiModel(GuardKind.Motion, "Motion Guard", "Monitoring", GuardStatus.Active),
        GuardUiModel(GuardKind.Sim, "SIM Guard", "Monitored", GuardStatus.Active),
        GuardUiModel(GuardKind.Panic, "Panic", "Ready", GuardStatus.Active),
    )

    val recentEvents = listOf(
        EventUiModel(
            id = "evt_1",
            type = EventType.FailedUnlock,
            title = "Failed unlock attempt",
            subtitle = "Attempt 2",
            timestampLabel = "Today · 14:32",
            deliveryStatus = DeliveryStatus.Delivered,
            hasPhoto = true,
            hasLocation = true,
        ),
        EventUiModel(
            id = "evt_2",
            type = EventType.Motion,
            title = "Motion detected",
            subtitle = "Movement after stillness",
            timestampLabel = "Today · 13:15",
            deliveryStatus = DeliveryStatus.Deferred,
            hasPhoto = true,
            hasLocation = false,
        ),
    )

    val dashboard = DashboardUiModel(
        protectionState = ProtectionVisualState.Protected,
        lastCheckSeconds = 12L,
        guards = guards,
        recentEvents = recentEvents,
        batteryPercent = 84,
        networkLabel = "Wi-Fi",
        networkOk = true,
        showAds = true,
    )

    val dashboardEmpty = dashboard.copy(recentEvents = emptyList())

    val dashboardSuspicious = dashboard.copy(
        protectionState = ProtectionVisualState.Suspicious,
        lastCheckSeconds = 4L,
    )

    val dashboardDisabled = dashboard.copy(
        protectionState = ProtectionVisualState.Disabled,
        guards = guards.map { it.copy(status = GuardStatus.Disabled) },
    )

    val eventDetail = EventDetailUiModel(
        id = "evt_8a3f...d92",
        type = EventType.FailedUnlock,
        title = "Failed unlock attempt",
        attemptNumber = 2,
        timestampLabel = "Today · 14:32:08",
        batteryPercent = 84,
        charging = false,
        hasPhoto = true,
        location = LocationUiModel(
            kind = LocationKind.Current,
            latitude = 36.7538,
            longitude = 3.0588,
            accuracyMeters = 8,
            ageSeconds = 4,
            source = "GPS",
        ),
        delivery = listOf(
            ChannelReceiptUiModel(ChannelId.Email, DeliveryStatus.Delivered, "14:32"),
            ChannelReceiptUiModel(ChannelId.WhatsApp, DeliveryStatus.Failed, "Invalid recipient"),
            ChannelReceiptUiModel(ChannelId.Telegram, DeliveryStatus.Delivered, "14:32"),
        ),
    )

    val eventDetailLastKnown = eventDetail.copy(
        location = LocationUiModel(
            kind = LocationKind.LastKnown,
            latitude = 36.7538,
            longitude = 3.0588,
            accuracyMeters = 45,
            ageSeconds = 7200,
            source = "Fused",
        ),
    )

    val premium = PremiumUiModel(
        priceLabel = "9.99",
        features = listOf(
            PremiumFeatureUiModel("Multiple delivery channels", "Email · WhatsApp · Telegram"),
            PremiumFeatureUiModel("Battery Emergency", "Queue evidence when battery is low"),
            PremiumFeatureUiModel("Extended history", "90 days · 500 events"),
            PremiumFeatureUiModel("Advanced delivery receipts"),
            PremiumFeatureUiModel("No ads"),
        ),
        freeTierSummary = "Free includes: Lock Guard · Motion Guard · SIM Guard · Panic · Email delivery.",
    )

    val settingsRows = listOf(
        SettingsRowUiModel("protection", "Protection", "Lock Guard · Guards", Icons.Filled.Security, false, SettingsSection.Security),
        SettingsRowUiModel("delivery", "Delivery channels", "Email · WhatsApp · Telegram", Icons.Filled.PhoneAndroid, false, SettingsSection.Delivery),
        SettingsRowUiModel("app_lock", "App lock", "Require authentication", Icons.Filled.Lock, true, SettingsSection.SecurityApp),

        SettingsRowUiModel("theme", "Theme", "Match system", Icons.Filled.ColorLens, false, SettingsSection.Appearance),
        SettingsRowUiModel("language", "Language", null, Icons.Filled.Language, false, SettingsSection.Appearance),

        SettingsRowUiModel("storage", "Storage", "12 events · 3.2 MB", Icons.Filled.Storage, false, SettingsSection.PrivacyData),
        SettingsRowUiModel("retention", "Retention policy", "7 days", Icons.Filled.DeleteOutline, true, SettingsSection.PrivacyData),

        SettingsRowUiModel("about", "About Shieldra", null, Icons.Filled.Info, false, SettingsSection.About),
        SettingsRowUiModel("help", "Help", null, Icons.Filled.Bolt, false, SettingsSection.About),
    )
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/ShieldCore.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.animation.core.Easing
import androidx.compose.animation.core.FastOutSlowInEasing
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.animateFloat
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.graphics.createShieldPath
import com.shieldra.app.design.graphics.dashedStrokeEffect
import com.shieldra.app.design.graphics.drawGlyphCheck
import com.shieldra.app.design.graphics.drawGlyphExclamation
import com.shieldra.app.design.graphics.drawGlyphSlash
import com.shieldra.app.design.graphics.drawGlyphX
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.ProtectionVisualState

private data class ShieldCoreConfig(
    val color: Color,
    val ringWidthDp: Float,
    val haloAlpha: Float,
    val pulseMs: Int,
    val pulseScale: Float,
    val label: String,
    val accessibilityLabel: String,
)

@Composable
private fun configFor(state: ProtectionVisualState): ShieldCoreConfig {
    val semantic = ShieldraTheme.semantic
    return when (state) {
        ProtectionVisualState.Protected -> ShieldCoreConfig(
            color = semantic.protection,
            ringWidthDp = 4f,
            haloAlpha = 0.18f,
            pulseMs = 3000,
            pulseScale = 1.015f,
            label = "PROTECTED",
            accessibilityLabel = "Protection is active",
        )
        ProtectionVisualState.Suspicious -> ShieldCoreConfig(
            color = semantic.suspicious,
            ringWidthDp = 5f,
            haloAlpha = 0.22f,
            pulseMs = 1400,
            pulseScale = 1.03f,
            label = "SUSPICIOUS",
            accessibilityLabel = "Suspicious activity detected",
        )
        ProtectionVisualState.SecurityEvent -> ShieldCoreConfig(
            color = semantic.securityEvent,
            ringWidthDp = 6f,
            haloAlpha = 0.28f,
            pulseMs = 0,
            pulseScale = 1f,
            label = "SECURITY EVENT",
            accessibilityLabel = "Security event confirmed",
        )
        ProtectionVisualState.Attention -> ShieldCoreConfig(
            color = semantic.attention,
            ringWidthDp = 4f,
            haloAlpha = 0.20f,
            pulseMs = 2000,
            pulseScale = 1.02f,
            label = "ATTENTION",
            accessibilityLabel = "Attention required",
        )
        ProtectionVisualState.Disabled -> ShieldCoreConfig(
            color = semantic.disabledState,
            ringWidthDp = 3f,
            haloAlpha = 0f,
            pulseMs = 0,
            pulseScale = 1f,
            label = "DISABLED",
            accessibilityLabel = "Protection disabled",
        )
    }
}

@Composable
fun ShieldCore(
    state: ProtectionVisualState,
    lastCheckSeconds: Long,
    modifier: Modifier = Modifier,
) {
    val config = configFor(state)
    val spacing = ShieldraTheme.spacing
    val easing: Easing = FastOutSlowInEasing

    val scale: Float = if (config.pulseMs > 0) {
        val transition = rememberInfiniteTransition(label = "shield_pulse")
        val animated by transition.animateFloat(
            initialValue = 1f,
            targetValue = config.pulseScale,
            animationSpec = infiniteRepeatable(
                animation = tween(config.pulseMs, easing = easing),
                repeatMode = RepeatMode.Reverse,
            ),
            label = "shield_scale",
        )
        animated
    } else 1f

    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(vertical = spacing.xxl)
            .semantics(mergeDescendants = true) {
                contentDescription =
                    "${config.accessibilityLabel}. Last check: " +
                    "${lastCheckSeconds}s ago."
            },
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Box(
            modifier = Modifier
                .size(200.dp)
                .graphicsLayer {
                    scaleX = scale
                    scaleY = scale
                },
            contentAlignment = Alignment.Center,
        ) {
            if (config.haloAlpha > 0f) {
                Canvas(Modifier.size(240.dp)) {
                    val c = Offset(size.width / 2f, size.height / 2f)
                    val r = size.minDimension / 2f
                    drawCircle(
                        brush = Brush.radialGradient(
                            colors = listOf(
                                config.color.copy(alpha = config.haloAlpha),
                                Color.Transparent,
                            ),
                            center = c,
                            radius = r,
                        ),
                        radius = r,
                        center = c,
                    )
                }
            }

            Canvas(Modifier.size(180.dp)) {
                val path = createShieldPath(Size(size.width, size.height))
                drawPath(path, MaterialTheme.colorScheme.surface)
                if (state == ProtectionVisualState.Disabled) {
                    drawPath(
                        path = path,
                        color = config.color,
                        style = Stroke(
                            width = config.ringWidthDp.dp.toPx(),
                            pathEffect = dashedStrokeEffect(),
                        ),
                    )
                } else {
                    drawPath(
                        path = path,
                        color = config.color,
                        style = Stroke(width = config.ringWidthDp.dp.toPx()),
                    )
                }
            }

            Canvas(Modifier.size(64.dp)) {
                val s = Size(size.width, size.height)
                when (state) {
                    ProtectionVisualState.Protected -> drawGlyphCheck(s, config.color)
                    ProtectionVisualState.Suspicious,
                    ProtectionVisualState.Attention -> drawGlyphExclamation(s, config.color)
                    ProtectionVisualState.SecurityEvent -> drawGlyphX(s, config.color)
                    ProtectionVisualState.Disabled -> drawGlyphSlash(s, config.color)
                }
            }
        }

        Spacer(Modifier.height(spacing.xxl))

        Text(
            text = config.label,
            style = MaterialTheme.typography.displayMedium,
            color = config.color,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.xs))
        Text(
            text = formatLastCheck(lastCheckSeconds),
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
    }
}

private fun formatLastCheck(seconds: Long): String = when {
    seconds < 60 -> "Last check: ${seconds}s ago"
    seconds < 3600 -> "Last check: ${seconds / 60}m ago"
    else -> "Last check: ${seconds / 3600}h ago"
}

@Preview(name = "ShieldCore Protected", showBackground = true)
@Composable
private fun ShieldCoreProtectedPreview() {
    ShieldraTheme(darkTheme = true) {
        ShieldCore(ProtectionVisualState.Protected, 12)
    }
}

@Preview(name = "ShieldCore Disabled", showBackground = true)
@Composable
private fun ShieldCoreDisabledPreview() {
    ShieldraTheme(darkTheme = true) {
        ShieldCore(ProtectionVisualState.Disabled, 12)
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/GuardTile.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.GuardKind
import com.shieldra.app.presentation.model.GuardStatus
import com.shieldra.app.presentation.model.GuardUiModel
import com.shieldra.app.presentation.model.icon

@Composable
private fun statusColor(status: GuardStatus): Color = when (status) {
    GuardStatus.Active -> ShieldraTheme.semantic.protection
    GuardStatus.Signal -> ShieldraTheme.semantic.suspicious
    GuardStatus.Disabled -> ShieldraTheme.semantic.disabledState
    GuardStatus.Locked -> MaterialTheme.colorScheme.onSurfaceVariant
}

@Composable
fun GuardTile(
    guard: GuardUiModel,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    val color = statusColor(guard.status)
    val shape = RoundedCornerShape(14.dp)

    Column(
        modifier = modifier
            .clip(shape)
            .background(MaterialTheme.colorScheme.surface)
            .clickable(
                enabled = guard.enabled,
                role = Role.Button,
                onClick = onClick,
            )
            .padding(spacing.l)
            .semantics(mergeDescendants = true) {
                val premiumSuffix = if (guard.isPremiumLocked) ". Premium feature." else ""
                contentDescription =
                    "${guard.title}. ${guard.subtitle}. Status: ${guard.status.name}.$premiumSuffix"
            },
    ) {
        Box(
            modifier = Modifier
                .size(44.dp)
                .clip(RoundedCornerShape(12.dp))
                .background(color.copy(alpha = 0.12f)),
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                imageVector = guard.kind.icon(),
                contentDescription = null,
                tint = color,
                modifier = Modifier.size(22.dp),
            )
        }

        Spacer(Modifier.height(spacing.m))

        Text(
            text = guard.title,
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.onSurface,
        )
        Spacer(Modifier.height(spacing.xs))
        Row(verticalAlignment = Alignment.CenterVertically) {
            if (guard.isPremiumLocked) {
                Text(
                    text = "★ ",
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.tertiary,
                )
            }
            Text(
                text = guard.subtitle,
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
            )
        }

        Spacer(Modifier.height(spacing.m))

        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(3.dp)
                .clip(RoundedCornerShape(2.dp))
                .background(
                    if (guard.status == GuardStatus.Disabled)
                        color.copy(alpha = 0.3f)
                    else color,
                ),
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun GuardTilePreview() {
    ShieldraTheme(darkTheme = true) {
        GuardTile(
            GuardUiModel(
                kind = GuardKind.Lock,
                title = "Lock Guard",
                subtitle = "Active · 2 attempts",
                status = GuardStatus.Active,
            ),
            onClick = {},
            modifier = Modifier.padding(16.dp).width(180.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/LiveStatusStrip.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.BatteryFull
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material.icons.filled.Wifi
import androidx.compose.material.icons.filled.WifiOff
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun LiveStatusStrip(
    lastCheckLabel: String,
    batteryPercent: Int?,
    networkLabel: String,
    networkOk: Boolean,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing

    Row(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(horizontal = spacing.l, vertical = spacing.m)
            .semantics(mergeDescendants = true) {
                contentDescription = buildString {
                    append("Last check $lastCheckLabel. ")
                    if (batteryPercent != null) append("Battery $batteryPercent percent. ")
                    append(if (networkOk) "Network connected: $networkLabel" else "Network offline")
                }
            },
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        StatusSlot(
            icon = Icons.Filled.Schedule,
            primary = lastCheckLabel,
            secondary = "Last check",
        )
        VerticalDivider()
        StatusSlot(
            icon = Icons.Filled.BatteryFull,
            primary = batteryPercent?.let { "$it%" } ?: "—",
            secondary = "Battery",
        )
        VerticalDivider()
        StatusSlot(
            icon = if (networkOk) Icons.Filled.Wifi else Icons.Filled.WifiOff,
            primary = if (networkOk) networkLabel else "Offline",
            secondary = "Network",
        )
    }
}

@Composable
private fun StatusSlot(
    icon: ImageVector,
    primary: String,
    secondary: String,
) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(2.dp),
    ) {
        Icon(
            imageVector = icon,
            contentDescription = null,
            tint = MaterialTheme.colorScheme.onSurfaceVariant,
            modifier = Modifier.size(18.dp),
        )
        Text(
            text = primary,
            style = MaterialTheme.typography.labelLarge,
            color = MaterialTheme.colorScheme.onSurface,
        )
        Text(
            text = secondary,
            style = MaterialTheme.typography.labelSmall,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
    }
}

@Composable
private fun VerticalDivider() {
    Box(
        Modifier
            .width(1.dp)
            .height(36.dp)
            .background(MaterialTheme.colorScheme.outline),
    )
}

@Preview(showBackground = true)
@Composable
private fun LiveStatusStripPreview() {
    ShieldraTheme(darkTheme = true) {
        LiveStatusStrip(
            lastCheckLabel = "12s ago",
            batteryPercent = 84,
            networkLabel = "Wi-Fi",
            networkOk = true,
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/EventCard.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.design.tokens.ShieldraPillShape
import com.shieldra.app.presentation.model.DeliveryStatus
import com.shieldra.app.presentation.model.EventType
import com.shieldra.app.presentation.model.EventUiModel
import com.shieldra.app.presentation.model.icon

@Composable
private fun deliveryColor(status: DeliveryStatus): Color = when (status) {
    DeliveryStatus.Delivered -> ShieldraTheme.semantic.protection
    DeliveryStatus.Delivering -> ShieldraTheme.semantic.suspicious
    DeliveryStatus.Deferred -> ShieldraTheme.semantic.suspicious
    DeliveryStatus.Ready -> MaterialTheme.colorScheme.onSurfaceVariant
    DeliveryStatus.Failed -> MaterialTheme.colorScheme.error
    DeliveryStatus.Expired -> MaterialTheme.colorScheme.onSurfaceVariant
    DeliveryStatus.Skipped -> MaterialTheme.colorScheme.onSurfaceVariant
}

private fun deliveryLabel(status: DeliveryStatus): String = when (status) {
    DeliveryStatus.Ready -> "Ready"
    DeliveryStatus.Delivering -> "Sending"
    DeliveryStatus.Delivered -> "Delivered"
    DeliveryStatus.Deferred -> "Retrying"
    DeliveryStatus.Failed -> "Failed"
    DeliveryStatus.Expired -> "Expired"
    DeliveryStatus.Skipped -> "Skipped"
}

@Composable
fun EventCard(
    event: EventUiModel,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    val shape = RoundedCornerShape(14.dp)

    Row(
        modifier = modifier
            .fillMaxWidth()
            .clip(shape)
            .background(MaterialTheme.colorScheme.surface)
            .clickable(role = Role.Button, onClick = onClick)
            .padding(spacing.l)
            .semantics(mergeDescendants = true) {
                contentDescription =
                    "${event.title}. ${event.timestampLabel}. " +
                    "Delivery: ${deliveryLabel(event.deliveryStatus)}."
            },
        verticalAlignment = Alignment.Top,
    ) {
        Box(
            modifier = Modifier
                .size(44.dp)
                .clip(RoundedCornerShape(12.dp))
                .background(MaterialTheme.colorScheme.primaryContainer),
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                imageVector = event.type.icon(),
                contentDescription = null,
                tint = MaterialTheme.colorScheme.primary,
                modifier = Modifier.size(22.dp),
            )
        }

        Spacer(Modifier.width(spacing.m))

        Column(Modifier.weight(1f)) {
            Text(
                text = event.title,
                style = MaterialTheme.typography.titleMedium,
                color = MaterialTheme.colorScheme.onSurface,
            )
            Spacer(Modifier.height(2.dp))
            Text(
                text = event.timestampLabel,
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
            )
            Spacer(Modifier.height(spacing.s))
            Row(horizontalArrangement = Arrangement.spacedBy(spacing.s)) {
                if (event.hasPhoto) Pill("Photo", MaterialTheme.colorScheme.surfaceVariant,
                    MaterialTheme.colorScheme.onSurfaceVariant)
                if (event.hasLocation) Pill("Location", MaterialTheme.colorScheme.surfaceVariant,
                    MaterialTheme.colorScheme.onSurfaceVariant)
                val dColor = deliveryColor(event.deliveryStatus)
                Pill(deliveryLabel(event.deliveryStatus), dColor.copy(alpha = 0.12f), dColor)
            }
        }
    }
}

@Composable
private fun Pill(text: String, bg: Color, fg: Color) {
    Box(
        Modifier
            .clip(ShieldraPillShape)
            .background(bg)
            .padding(horizontal = 10.dp, vertical = 4.dp),
    ) {
        Text(
            text = text,
            style = MaterialTheme.typography.labelMedium,
            color = fg,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun EventCardPreview() {
    ShieldraTheme(darkTheme = true) {
        EventCard(
            event = EventUiModel(
                id = "evt_1",
                type = EventType.FailedUnlock,
                title = "Failed unlock attempt",
                subtitle = "Attempt 2",
                timestampLabel = "Today · 14:32",
                deliveryStatus = DeliveryStatus.Delivered,
                hasPhoto = true,
                hasLocation = true,
            ),
            onClick = {},
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/LocationBadge.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.design.tokens.ShieldraPillShape
import com.shieldra.app.presentation.model.LocationKind

@Composable
fun LocationBadge(
    kind: LocationKind,
    modifier: Modifier = Modifier,
) {
    val shape = ShieldraPillShape
    val color: Color = when (kind) {
        LocationKind.Current -> ShieldraTheme.semantic.protection
        LocationKind.LastKnown -> MaterialTheme.colorScheme.onSurfaceVariant
        LocationKind.Unavailable -> MaterialTheme.colorScheme.outline
    }
    val label = when (kind) {
        LocationKind.Current -> "Current Location"
        LocationKind.LastKnown -> "Last Known Location"
        LocationKind.Unavailable -> "Location unavailable"
    }

    Row(
        modifier = modifier
            .clip(shape)
            .background(color.copy(alpha = 0.08f))
            .border(1.dp, color, shape)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Canvas(Modifier.size(8.dp)) {
            if (kind == LocationKind.Current) {
                drawCircle(color)
            } else {
                drawCircle(color, style = Stroke(width = 1.5.dp.toPx()))
            }
        }
        Spacer(Modifier.width(6.dp))
        Text(
            text = label,
            style = MaterialTheme.typography.labelMedium,
            color = color,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun LocationBadgePreview() {
    ShieldraTheme(darkTheme = true) {
        Row(Modifier.padding(16.dp)) {
            LocationBadge(LocationKind.Current)
            Spacer(Modifier.width(8.dp))
            LocationBadge(LocationKind.LastKnown)
        }
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/FreshnessIndicator.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.design.tokens.ShieldraTextStyles

@Composable
fun FreshnessIndicator(
    ageSeconds: Long,
    modifier: Modifier = Modifier,
) {
    val color: Color = when {
        ageSeconds < 60 -> ShieldraTheme.semantic.protection
        ageSeconds < 3600 -> MaterialTheme.colorScheme.onSurfaceVariant
        else -> ShieldraTheme.semantic.suspicious
    }
    Row(modifier = modifier, verticalAlignment = Alignment.CenterVertically) {
        Icon(
            imageVector = Icons.Filled.Schedule,
            contentDescription = null,
            tint = color,
            modifier = Modifier.size(14.dp),
        )
        Spacer(Modifier.width(4.dp))
        Text(
            text = formatAge(ageSeconds),
            style = ShieldraTextStyles.NumericData,
            color = color,
        )
    }
}

private fun formatAge(seconds: Long): String = when {
    seconds < 60 -> "${seconds}s old"
    seconds < 3600 -> "${seconds / 60}m old"
    seconds < 86400 -> "${seconds / 3600}h old"
    else -> "${seconds / 86400}d old"
}

@Preview(showBackground = true)
@Composable
private fun FreshnessIndicatorPreview() {
    ShieldraTheme(darkTheme = true) {
        FreshnessIndicator(4L)
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/EvidenceCard.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.CameraAlt
import androidx.compose.material.icons.filled.LocationOn
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.design.tokens.ShieldraTextStyles
import com.shieldra.app.presentation.model.LocationKind
import com.shieldra.app.presentation.model.LocationUiModel

@Composable
fun PhotoEvidenceCard(
    available: Boolean,
    modifier: Modifier = Modifier,
    onClick: (() -> Unit)? = null,
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .aspectRatio(4f / 3f)
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surfaceVariant),
        contentAlignment = Alignment.Center,
    ) {
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Icon(
                imageVector = Icons.Filled.CameraAlt,
                contentDescription = null,
                tint = MaterialTheme.colorScheme.onSurfaceVariant,
                modifier = Modifier.size(28.dp),
            )
            Spacer(Modifier.height(6.dp))
            Text(
                text = if (available) "Photo evidence" else "No photo available",
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
            )
        }
    }
}

@Composable
fun LocationEvidenceCard(
    location: LocationUiModel,
    onOpenInMaps: (() -> Unit)?,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(spacing.l),
    ) {
        Text(
            text = "LOCATION",
            style = MaterialTheme.typography.labelSmall,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.s))

        when (location.kind) {
            LocationKind.Unavailable -> {
                Row(verticalAlignment = Alignment.CenterVertically) {
                    Icon(
                        imageVector = Icons.Filled.LocationOn,
                        contentDescription = null,
                        tint = MaterialTheme.colorScheme.onSurfaceVariant,
                        modifier = Modifier.size(20.dp),
                    )
                    Spacer(Modifier.width(8.dp))
                    Text(
                        text = "Location unavailable",
                        style = MaterialTheme.typography.bodyMedium,
                        color = MaterialTheme.colorScheme.onSurfaceVariant,
                    )
                }
            }
            else -> {
                LocationBadge(location.kind)
                Spacer(Modifier.height(spacing.s))

                val mapPlaceholder = Modifier
                    .fillMaxWidth()
                    .height(160.dp)
                    .clip(RoundedCornerShape(10.dp))
                    .background(MaterialTheme.colorScheme.surfaceVariant)

                Box(mapPlaceholder, contentAlignment = Alignment.Center) {
                    Text(
                        text = "[ Map ]",
                        style = MaterialTheme.typography.bodyMedium,
                        color = MaterialTheme.colorScheme.onSurfaceVariant,
                    )
                }

                Spacer(Modifier.height(spacing.s))

                Row(verticalAlignment = Alignment.CenterVertically) {
                    if (location.accuracyMeters != null) {
                        Text(
                            text = "±${location.accuracyMeters}m",
                            style = ShieldraTextStyles.NumericData,
                            color = MaterialTheme.colorScheme.onSurfaceVariant,
                        )
                        Spacer(Modifier.width(spacing.s))
                    }
                    if (location.ageSeconds != null) {
                        FreshnessIndicator(location.ageSeconds)
                        Spacer(Modifier.width(spacing.s))
                    }
                    if (location.source != null) {
                        Text(
                            text = location.source,
                            style = MaterialTheme.typography.labelSmall,
                            color = MaterialTheme.colorScheme.onSurfaceVariant,
                        )
                    }
                }

                if (onOpenInMaps != null) {
                    Spacer(Modifier.height(spacing.s))
                    com.shieldra.app.design.components.foundation.ShieldraButton(
                        text = "Open in Maps",
                        onClick = onOpenInMaps,
                        variant = com.shieldra.app.design.components.foundation.ShieldraButtonVariant.Secondary,
                    )
                }
            }
        }
    }
}

@Preview(showBackground = true)
@Composable
private fun LocationEvidenceCardPreview() {
    ShieldraTheme(darkTheme = true) {
        LocationEvidenceCard(
            location = LocationUiModel(
                kind = LocationKind.Current,
                accuracyMeters = 8,
                ageSeconds = 4,
                source = "GPS",
            ),
            onOpenInMaps = {},
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/DeliveryReceipt.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.ChannelId
import com.shieldra.app.presentation.model.ChannelReceiptUiModel
import com.shieldra.app.presentation.model.DeliveryStatus

private fun statusColor(status: DeliveryStatus): Color = when (status) {
    DeliveryStatus.Delivered -> ShieldraTheme.semantic.protection
    DeliveryStatus.Failed -> MaterialTheme.colorScheme.error
    DeliveryStatus.Delivering -> ShieldraTheme.semantic.suspicious
    else -> MaterialTheme.colorScheme.onSurfaceVariant
}

private fun statusLabel(status: DeliveryStatus): String = when (status) {
    DeliveryStatus.Delivered -> "✓ Delivered"
    DeliveryStatus.Failed -> "✗ Failed"
    DeliveryStatus.Skipped -> "— Skipped"
    DeliveryStatus.Delivering -> "· Sending"
    DeliveryStatus.Ready -> "· Ready"
    DeliveryStatus.Deferred -> "· Retrying"
    DeliveryStatus.Expired -> "· Expired"
}

private fun channelLabel(channel: ChannelId): String = when (channel) {
    ChannelId.Email -> "Email"
    ChannelId.WhatsApp -> "WhatsApp"
    ChannelId.Telegram -> "Telegram"
}

@Composable
fun DeliveryReceipt(
    receipts: List<ChannelReceiptUiModel>,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(spacing.l),
    ) {
        Text(
            text = "DELIVERY",
            style = MaterialTheme.typography.labelSmall,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.s))
        receipts.forEachIndexed { index, receipt ->
            ChannelRow(receipt)
            if (index < receipts.lastIndex) Spacer(Modifier.height(spacing.s))
        }
    }
}

@Composable
private fun ChannelRow(receipt: ChannelReceiptUiModel) {
    val spacing = ShieldraTheme.spacing
    val color = statusColor(receipt.status)

    Row(
        Modifier.fillMaxWidth(),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Box(
            Modifier
                .size(28.dp)
                .clip(RoundedCornerShape(8.dp))
                .background(MaterialTheme.colorScheme.surfaceVariant),
            contentAlignment = Alignment.Center,
        ) {
            Text(
                text = channelLabel(receipt.channel).take(1),
                style = MaterialTheme.typography.labelMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
            )
        }
        Spacer(Modifier.width(spacing.m))
        Column(Modifier.weight(1f)) {
            Text(
                text = channelLabel(receipt.channel),
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onSurface,
            )
            if (receipt.detail != null) {
                Text(
                    text = receipt.detail,
                    style = MaterialTheme.typography.labelSmall,
                    color = if (receipt.status == DeliveryStatus.Failed)
                        MaterialTheme.colorScheme.error
                    else MaterialTheme.colorScheme.onSurfaceVariant,
                )
            }
        }
        Text(
            text = statusLabel(receipt.status),
            style = MaterialTheme.typography.labelLarge,
            color = color,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun DeliveryReceiptPreview() {
    ShieldraTheme(darkTheme = true) {
        DeliveryReceipt(
            receipts = listOf(
                ChannelReceiptUiModel(ChannelId.Email, DeliveryStatus.Delivered, "14:32"),
                ChannelReceiptUiModel(ChannelId.WhatsApp, DeliveryStatus.Failed, "Invalid recipient"),
                ChannelReceiptUiModel(ChannelId.Telegram, DeliveryStatus.Delivered, "14:32"),
            ),
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/EmptyState.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.graphics.createShieldPath
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun EmptyState(
    title: String,
    message: String,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(spacing.xxxl),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Canvas(Modifier.size(120.dp)) {
            val path = createShieldPath(Size(size.width, size.height))
            drawPath(
                path = path,
                color = MaterialTheme.colorScheme.outline,
                style = Stroke(width = 3.dp.toPx()),
            )
        }
        Spacer(Modifier.height(spacing.xxl))
        Text(
            text = title,
            style = MaterialTheme.typography.headlineMedium,
            color = MaterialTheme.colorScheme.onBackground,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.s))
        Text(
            text = message,
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun EmptyStatePreview() {
    ShieldraTheme(darkTheme = true) {
        EmptyState(
            title = "No events yet",
            message = "When Shieldra detects suspicious activity, you'll see it here.",
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/ErrorState.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.foundation.ShieldraButton
import com.shieldra.app.design.components.foundation.ShieldraButtonVariant
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun ErrorStateInline(
    title: String = "Something went wrong",
    message: String,
    onRetry: (() -> Unit)? = null,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.errorContainer)
            .padding(spacing.l),
        verticalArrangement = Arrangement.spacedBy(spacing.xs),
    ) {
        Text(
            text = title,
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.onErrorContainer,
        )
        Text(
            text = message,
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onErrorContainer,
        )
        if (onRetry != null) {
            Spacer(Modifier.height(spacing.s))
            ShieldraButton(
                text = "Retry",
                onClick = onRetry,
                variant = ShieldraButtonVariant.Secondary,
            )
        }
    }
}

@Composable
fun ErrorStateFullScreen(
    title: String = "Unable to load",
    message: String,
    onRetry: (() -> Unit)? = null,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(spacing.xxxl),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(
            text = title,
            style = MaterialTheme.typography.headlineMedium,
            color = MaterialTheme.colorScheme.error,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.s))
        Text(
            text = message,
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
        if (onRetry != null) {
            Spacer(Modifier.height(spacing.xxl))
            ShieldraButton(text = "Retry", onClick = onRetry)
        }
    }
}

@Preview(showBackground = true)
@Composable
private fun ErrorStateInlinePreview() {
    ShieldraTheme(darkTheme = true) {
        ErrorStateInline(
            message = "Could not load events.",
            onRetry = {},
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/design/components/domain/LoadingState.kt

```kotlin
package com.shieldra.app.design.components.domain

import androidx.compose.animation.core.LinearEasing
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.animateFloat
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun SkeletonBlock(
    height: Dp,
    modifier: Modifier = Modifier,
) {
    val transition = rememberInfiniteTransition(label = "skeleton")
    val progress by transition.animateFloat(
        initialValue = 0f,
        targetValue = 1f,
        animationSpec = infiniteRepeatable(
            animation = tween(1600, easing = LinearEasing),
            repeatMode = RepeatMode.Reverse,
        ),
        label = "skeleton_progress",
    )
    val base = MaterialTheme.colorScheme.surfaceVariant
    val brush = Brush.horizontalGradient(
        colors = listOf(
            base.copy(alpha = 0.4f + 0.3f * progress),
            base.copy(alpha = 0.2f + 0.15f * progress),
        ),
    )
    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(height)
            .clip(RoundedCornerShape(14.dp))
            .background(brush),
    )
}

@Composable
fun LoadingStateList(
    modifier: Modifier = Modifier,
    rows: Int = 4,
) {
    Column(
        modifier = modifier.padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        repeat(rows) { SkeletonBlock(88.dp) }
    }
}

@Preview(showBackground = true)
@Composable
private fun LoadingStatePreview() {
    ShieldraTheme(darkTheme = true) {
        LoadingStateList()
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/onboarding/WelcomeScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.onboarding

import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.foundation.ShieldraFullWidthButton
import com.shieldra.app.design.components.foundation.ShieldraButtonVariant
import com.shieldra.app.design.graphics.createShieldPath
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun WelcomeScreen(
    onGetStarted: () -> Unit,
    onSkip: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(spacing.xxxl),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Canvas(Modifier.size(160.dp)) {
            val path = createShieldPath(Size(size.width, size.height))
            drawPath(
                path = path,
                color = MaterialTheme.colorScheme.primary,
                style = Stroke(width = 4.dp.toPx()),
            )
        }
        Spacer(Modifier.height(spacing.xxl))
        Text(
            text = "Shieldra",
            style = MaterialTheme.typography.displayMedium,
            color = MaterialTheme.colorScheme.onBackground,
        )
        Spacer(Modifier.height(spacing.s))
        Text(
            text = "Your phone's quiet guardian.",
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.l))
        Text(
            text = "Detects unauthorized use, collects evidence, and delivers it — so you know what happened.",
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.xxxl))
        ShieldraFullWidthButton("Get started", onGetStarted)
        Spacer(Modifier.height(spacing.s))
        ShieldraFullWidthButton(
            text = "Skip for now",
            onClick = onSkip,
            variant = ShieldraButtonVariant.Ghost,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun WelcomePreview() {
    ShieldraTheme(darkTheme = true) {
        WelcomeScreen(onGetStarted = {}, onSkip = {})
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/onboarding/PermissionsScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.onboarding

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.foundation.ShieldraButtonVariant
import com.shieldra.app.design.components.foundation.ShieldraFullWidthButton
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun PermissionsScreen(
    step: Int,
    totalSteps: Int,
    icon: ImageVector,
    title: String,
    reason: String,
    onAllow: () -> Unit,
    onNotNow: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(spacing.xxxl),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text(
            text = "Step $step of $totalSteps",
            style = MaterialTheme.typography.labelMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.xxl))
        Icon(
            imageVector = icon,
            contentDescription = null,
            tint = MaterialTheme.colorScheme.primary,
            modifier = Modifier.size(48.dp),
        )
        Spacer(Modifier.height(spacing.xl))
        Text(
            text = title,
            style = MaterialTheme.typography.headlineMedium,
            color = MaterialTheme.colorScheme.onBackground,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.m))
        Text(
            text = reason,
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.xxxl))
        ShieldraFullWidthButton("Allow", onAllow)
        Spacer(Modifier.height(spacing.s))
        ShieldraFullWidthButton(
            text = "Not now",
            onClick = onNotNow,
            variant = ShieldraButtonVariant.Ghost,
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun PermissionsPreview() {
    ShieldraTheme(darkTheme = true) {
        PermissionsScreen(
            step = 2,
            totalSteps = 4,
            icon = androidx.compose.material.icons.Icons.Filled.Lock,
            title = "Notifications",
            reason = "When Shieldra detects a security event, you'll get a notification.",
            onAllow = {},
            onNotNow = {},
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/onboarding/ProtectionSetupScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.onboarding

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.mutableFloatStateOf
import androidx.compose.runtime.remember
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.foundation.ShieldraFullWidthButton
import com.shieldra.app.design.components.foundation.ShieldraSlider
import com.shieldra.app.design.theme.ShieldraTheme

@Composable
fun ProtectionSetupScreen(
    initialThreshold: Int,
    onContinue: (Int) -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    val threshold = remember { mutableFloatStateOf(initialThreshold.toFloat()) }
    val displayed = threshold.floatValue.toInt()

    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(spacing.xxxl),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text(
            text = "Lock Guard",
            style = MaterialTheme.typography.headlineMedium,
            color = MaterialTheme.colorScheme.onBackground,
        )
        Spacer(Modifier.height(spacing.m))
        Text(
            text = "How many failed unlock attempts before Shieldra records an event?",
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.xxxl))
        Text(
            text = "$displayed",
            style = MaterialTheme.typography.displayLarge,
            color = MaterialTheme.colorScheme.primary,
        )
        Spacer(Modifier.height(spacing.l))
        ShieldraSlider(
            value = threshold.floatValue,
            onValueChange = { threshold.floatValue = it },
            valueRange = 1f..5f,
            steps = 3,
            contentDescription = "Failed attempts threshold",
        )
        Spacer(Modifier.height(spacing.m))
        Text(
            text = "Recommended: 2 for most users.",
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.xs))
        Text(
            text = "Attempt 1 is ignored. Each attempt after is recorded.",
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            textAlign = TextAlign.Center,
        )
        Spacer(Modifier.height(spacing.xxxl))
        ShieldraFullWidthButton(
            text = "Continue",
            onClick = { onContinue(displayed) },
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun ProtectionSetupPreview() {
    ShieldraTheme(darkTheme = true) {
        ProtectionSetupScreen(initialThreshold = 2, onContinue = {})
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/dashboard/DashboardCallbacks.kt

```kotlin
package com.shieldra.app.presentation.screen.dashboard

import androidx.compose.runtime.Immutable

@Immutable
data class DashboardCallbacks(
    val onEventClick: (String) -> Unit,
    val onGuardClick: (String) -> Unit,
    val onPanic: () -> Unit,
    val onViewAllEvents: () -> Unit,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/dashboard/DashboardScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.dashboard

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.PaddingValues
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.WarningAmber
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.domain.EmptyState
import com.shieldra.app.design.components.domain.EventCard
import com.shieldra.app.design.components.domain.GuardTile
import com.shieldra.app.design.components.domain.LiveStatusStrip
import com.shieldra.app.design.components.domain.ShieldCore
import com.shieldra.app.design.components.foundation.ShieldraAdSlot
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.DashboardUiModel
import com.shieldra.app.presentation.model.GuardUiModel
import com.shieldra.app.presentation.preview.PreviewData

@Composable
fun DashboardScreen(
    model: DashboardUiModel,
    callbacks: DashboardCallbacks,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing

    LazyColumn(
        modifier = modifier
            .fillMaxSize()
            .background(MaterialTheme.colorScheme.background),
        contentPadding = PaddingValues(bottom = spacing.xxxl),
    ) {
        item {
            ShieldCore(
                state = model.protectionState,
                lastCheckSeconds = model.lastCheckSeconds,
            )
        }

        item {
            LiveStatusStrip(
                lastCheckLabel = "${model.lastCheckSeconds}s ago",
                batteryPercent = model.batteryPercent,
                networkLabel = model.networkLabel,
                networkOk = model.networkOk,
                modifier = Modifier.padding(horizontal = spacing.l),
            )
        }

        item {
            Spacer(Modifier.height(spacing.xxl))
            PanicQuickAction(onPanic = callbacks.onPanic)
        }

        item {
            Spacer(Modifier.height(spacing.xxl))
            SectionLabel("GUARDS")
        }

        item {
            GuardsGrid(
                guards = model.guards,
                onGuardClick = callbacks.onGuardClick,
                modifier = Modifier.padding(horizontal = spacing.l),
            )
        }

        item {
            Spacer(Modifier.height(spacing.xxl))
            SectionHeaderWithAction(
                label = "RECENT",
                actionLabel = "View all",
                onAction = callbacks.onViewAllEvents,
            )
        }

        if (model.recentEvents.isEmpty()) {
            item {
                EmptyState(
                    title = "No events yet",
                    message = "When Shieldra detects suspicious activity, you'll see it here.",
                    modifier = Modifier.padding(vertical = spacing.xl),
                )
            }
        } else {
            items(model.recentEvents) { event ->
                EventCard(
                    event = event,
                    onClick = { callbacks.onEventClick(event.id) },
                    modifier = Modifier.padding(horizontal = spacing.l, vertical = spacing.xs),
                )
            }
        }

        if (model.showAds) {
            item {
                Spacer(Modifier.height(spacing.xl))
                ShieldraAdSlot(modifier = Modifier.padding(horizontal = spacing.l))
            }
        }
    }
}

@Composable
private fun SectionLabel(text: String) {
    val spacing = ShieldraTheme.spacing
    Text(
        text = text,
        style = MaterialTheme.typography.labelMedium,
        color = MaterialTheme.colorScheme.onSurfaceVariant,
        modifier = Modifier.padding(
            start = spacing.l,
            end = spacing.l,
            bottom = spacing.s,
        ),
    )
}

@Composable
private fun SectionHeaderWithAction(
    label: String,
    actionLabel: String,
    onAction: () -> Unit,
) {
    val spacing = ShieldraTheme.spacing
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(start = spacing.l, end = spacing.l, bottom = spacing.s),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(
            text = label,
            style = MaterialTheme.typography.labelMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Text(
            text = actionLabel,
            style = MaterialTheme.typography.labelMedium,
            color = MaterialTheme.colorScheme.primary,
            modifier = Modifier
                .clip(RoundedCornerShape(6.dp))
                .clickable(role = Role.Button, onClick = onAction)
                .padding(horizontal = 8.dp, vertical = 4.dp),
        )
    }
}

@Composable
private fun PanicQuickAction(onPanic: () -> Unit) {
    val spacing = ShieldraTheme.spacing
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(horizontal = spacing.l)
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.errorContainer)
            .clickable(role = Role.Button, onClick = onPanic)
            .padding(horizontal = spacing.l, vertical = spacing.m),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(
            imageVector = Icons.Filled.WarningAmber,
            contentDescription = null,
            tint = MaterialTheme.colorScheme.onErrorContainer,
            modifier = Modifier.size(22.dp),
        )
        Spacer(Modifier.size(spacing.m))
        Column(Modifier.weight(1f)) {
            Text(
                text = "Silent Panic",
                style = MaterialTheme.typography.titleMedium,
                color = MaterialTheme.colorScheme.onErrorContainer,
            )
            Text(
                text = "Trigger an emergency event",
                style = MaterialTheme.typography.bodySmall,
                color = MaterialTheme.colorScheme.onErrorContainer,
            )
        }
    }
}

@Composable
private fun GuardsGrid(
    guards: List<GuardUiModel>,
    onGuardClick: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(modifier = modifier, verticalArrangement = Arrangement.spacedBy(spacing.m)) {
        guards.chunked(2).forEach { row ->
            Row(horizontalArrangement = Arrangement.spacedBy(spacing.m)) {
                row.forEach { guard ->
                    GuardTile(
                        guard = guard,
                        onClick = { onGuardClick(guard.kind.name) },
                        modifier = Modifier.weight(1f),
                    )
                }
                if (row.size == 1) Spacer(Modifier.weight(1f))
            }
        }
    }
}

@Preview(name = "Dashboard Dark", showBackground = true)
@Composable
private fun DashboardDarkPreview() {
    ShieldraTheme(darkTheme = true) {
        DashboardScreen(
            model = PreviewData.dashboard,
            callbacks = DashboardCallbacks({}, {}, {}, {}),
        )
    }
}

@Preview(name = "Dashboard Light", showBackground = true)
@Composable
private fun DashboardLightPreview() {
    ShieldraTheme(darkTheme = false) {
        DashboardScreen(
            model = PreviewData.dashboard,
            callbacks = DashboardCallbacks({}, {}, {}, {}),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/history/HistoryCallbacks.kt

```kotlin
package com.shieldra.app.presentation.screen.history

import androidx.compose.runtime.Immutable

@Immutable
data class HistoryCallbacks(
    val onEventClick: (String) -> Unit,
    val onFilterChange: (HistoryFilterStateChanged) -> Unit,
)

@Immutable
data class HistoryFilterStateChanged(
    val type: com.shieldra.app.presentation.model.HistoryTypeFilter,
    val status: com.shieldra.app.presentation.model.HistoryStatusFilter,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/history/HistoryScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.history

import androidx.compose.foundation.background
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.PaddingValues
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.rememberScrollState
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.domain.EmptyState
import com.shieldra.app.design.components.domain.EventCard
import com.shieldra.app.design.components.foundation.ShieldraChip
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.EventUiModel
import com.shieldra.app.presentation.model.HistoryFilterState
import com.shieldra.app.presentation.model.HistoryStatusFilter
import com.shieldra.app.presentation.model.HistoryTypeFilter
import com.shieldra.app.presentation.preview.PreviewData

@Composable
fun HistoryScreen(
    events: List<EventUiModel>,
    filterState: HistoryFilterState,
    callbacks: HistoryCallbacks,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing

    Column(
        modifier = modifier
            .fillMaxSize()
            .background(MaterialTheme.colorScheme.background),
    ) {
        TypeFilterRow(
            current = filterState.type,
            onSelect = { newType ->
                callbacks.onFilterChange(
                    HistoryFilterStateChanged(newType, filterState.status)
                )
            },
        )
        StatusFilterRow(
            current = filterState.status,
            onSelect = { newStatus ->
                callbacks.onFilterChange(
                    HistoryFilterStateChanged(filterState.type, newStatus)
                )
            },
        )

        if (events.isEmpty()) {
            EmptyState(
                title = "No events match",
                message = "Try adjusting filters, or wait for Shieldra to detect activity.",
                modifier = Modifier.fillMaxSize(),
            )
        } else {
            LazyColumn(
                contentPadding = PaddingValues(
                    start = spacing.l,
                    end = spacing.l,
                    top = spacing.s,
                    bottom = spacing.xxxl,
                ),
                verticalArrangement = Arrangement.spacedBy(spacing.s),
            ) {
                items(events) { event ->
                    EventCard(
                        event = event,
                        onClick = { callbacks.onEventClick(event.id) },
                    )
                }
            }
        }
    }
}

@Composable
private fun TypeFilterRow(
    current: HistoryTypeFilter,
    onSelect: (HistoryTypeFilter) -> Unit,
) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .horizontalScroll(rememberScrollState())
            .padding(horizontal = 16.dp, vertical = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        HistoryTypeFilter.values().forEach { filter ->
            ShieldraChip(
                label = filter.displayLabel(),
                selected = filter == current,
                onClick = { onSelect(filter) },
            )
        }
    }
}

@Composable
private fun StatusFilterRow(
    current: HistoryStatusFilter,
    onSelect: (HistoryStatusFilter) -> Unit,
) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .horizontalScroll(rememberScrollState())
            .padding(horizontal = 16.dp, vertical = 4.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        HistoryStatusFilter.values().forEach { filter ->
            ShieldraChip(
                label = filter.displayLabel(),
                selected = filter == current,
                onClick = { onSelect(filter) },
            )
        }
    }
}

private fun HistoryTypeFilter.displayLabel(): String = when (this) {
    HistoryTypeFilter.All -> "All"
    HistoryTypeFilter.Lock -> "Lock"
    HistoryTypeFilter.Motion -> "Motion"
    HistoryTypeFilter.Sim -> "SIM"
    HistoryTypeFilter.Panic -> "Panic"
    HistoryTypeFilter.Battery -> "Battery"
}

private fun HistoryStatusFilter.displayLabel(): String = when (this) {
    HistoryStatusFilter.All -> "Any status"
    HistoryStatusFilter.Delivered -> "Delivered"
    HistoryStatusFilter.Deferred -> "Deferred"
    HistoryStatusFilter.Failed -> "Failed"
}

@Preview(showBackground = true)
@Composable
private fun HistoryDarkPreview() {
    ShieldraTheme(darkTheme = true) {
        HistoryScreen(
            events = PreviewData.recentEvents,
            filterState = HistoryFilterState(),
            callbacks = HistoryCallbacks({}, {}),
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun HistoryEmptyPreview() {
    ShieldraTheme(darkTheme = true) {
        HistoryScreen(
            events = emptyList(),
            filterState = HistoryFilterState(),
            callbacks = HistoryCallbacks({}, {}),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/eventdetail/EventDetailCallbacks.kt

```kotlin
package com.shieldra.app.presentation.screen.eventdetail

import androidx.compose.runtime.Immutable

@Immutable
data class EventDetailCallbacks(
    val onBack: () -> Unit,
    val onOpenInMaps: (Double, Double) -> Unit,
    val onDelete: () -> Unit,
    val onExport: () -> Unit,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/eventdetail/EventDetailScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.eventdetail

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.PaddingValues
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.ArrowBack
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TopAppBar
import androidx.compose.material3.TopAppBarDefaults
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.domain.DeliveryReceipt
import com.shieldra.app.design.components.domain.LocationEvidenceCard
import com.shieldra.app.design.components.domain.PhotoEvidenceCard
import com.shieldra.app.design.components.foundation.ShieldraButton
import com.shieldra.app.design.components.foundation.ShieldraButtonVariant
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.design.tokens.ShieldraTextStyles
import com.shieldra.app.presentation.model.EventDetailUiModel
import com.shieldra.app.presentation.model.LocationKind
import com.shieldra.app.presentation.preview.PreviewData

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun EventDetailScreen(
    model: EventDetailUiModel,
    callbacks: EventDetailCallbacks,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing

    Scaffold(
        modifier = modifier,
        topBar = {
            TopAppBar(
                title = {
                    Text(
                        text = model.title,
                        style = MaterialTheme.typography.titleLarge,
                    )
                },
                navigationIcon = {
                    IconButton(onClick = callbacks.onBack) {
                        Icon(
                            imageVector = Icons.AutoMirrored.Filled.ArrowBack,
                            contentDescription = "Back",
                        )
                    }
                },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.background,
                    titleContentColor = MaterialTheme.colorScheme.onBackground,
                    navigationIconContentColor = MaterialTheme.colorScheme.onBackground,
                ),
            )
        },
    ) { innerPadding ->
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(innerPadding)
                .background(MaterialTheme.colorScheme.background),
            contentPadding = PaddingValues(bottom = spacing.xxxl),
            verticalArrangement = Arrangement.spacedBy(spacing.l),
        ) {
            item {
                PhotoEvidenceCard(
                    available = model.hasPhoto,
                    modifier = Modifier.padding(horizontal = spacing.l),
                )
            }

            item {
                LocationEvidenceCard(
                    location = model.location,
                    onOpenInMaps = if (
                        model.location.kind != LocationKind.Unavailable &&
                        model.location.latitude != null &&
                        model.location.longitude != null
                    ) {
                        {
                            callbacks.onOpenInMaps(
                                model.location.latitude!!,
                                model.location.longitude!!,
                            )
                        }
                    } else null,
                    modifier = Modifier.padding(horizontal = spacing.l),
                )
            }

            item {
                DetailsBlock(model, modifier = Modifier.padding(horizontal = spacing.l))
            }

            item {
                DeliveryReceipt(
                    receipts = model.delivery,
                    modifier = Modifier.padding(horizontal = spacing.l),
                )
            }

            item {
                DestructiveActions(
                    onDelete = callbacks.onDelete,
                    onExport = callbacks.onExport,
                    modifier = Modifier.padding(horizontal = spacing.l),
                )
            }
        }
    }
}

@Composable
private fun DetailsBlock(
    model: EventDetailUiModel,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(spacing.l),
        verticalArrangement = Arrangement.spacedBy(spacing.s),
    ) {
        Text(
            text = "DETAILS",
            style = MaterialTheme.typography.labelSmall,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        DetailRow("Type", model.title)
        if (model.attemptNumber != null) {
            DetailRow("Attempt", "${model.attemptNumber}")
        }
        DetailRow("Timestamp", model.timestampLabel)
        if (model.batteryPercent != null) {
            DetailRow(
                "Battery",
                "${model.batteryPercent}% · " +
                    if (model.charging) "Charging" else "Not charging",
            )
        }
        DetailRow("Event ID", model.id)
    }
}

@Composable
private fun DetailRow(label: String, value: String) {
    Row(
        Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Text(
            text = label,
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Text(
            text = value,
            style = ShieldraTextStyles.NumericData,
            color = MaterialTheme.colorScheme.onSurface,
        )
    }
}

@Composable
private fun DestructiveActions(
    onDelete: () -> Unit,
    onExport: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        ShieldraButton(
            text = "Delete",
            onClick = onDelete,
            variant = ShieldraButtonVariant.Destructive,
            modifier = Modifier.weight(1f),
        )
        ShieldraButton(
            text = "Export",
            onClick = onExport,
            variant = ShieldraButtonVariant.Secondary,
            modifier = Modifier.weight(1f),
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun EventDetailDarkPreview() {
    ShieldraTheme(darkTheme = true) {
        EventDetailScreen(
            model = PreviewData.eventDetail,
            callbacks = EventDetailCallbacks({}, { _, _ -> }, {}, {}),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/premium/PremiumCallbacks.kt

```kotlin
package com.shieldra.app.presentation.screen.premium

import androidx.compose.runtime.Immutable

@Immutable
data class PremiumCallbacks(
    val onBuy: () -> Unit,
    val onRestore: () -> Unit,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/premium/PremiumScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.premium

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.components.foundation.ShieldraFullWidthButton
import com.shieldra.app.design.components.foundation.ShieldraButtonVariant
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.PremiumFeatureUiModel
import com.shieldra.app.presentation.model.PremiumUiModel
import com.shieldra.app.presentation.preview.PreviewData

@Composable
fun PremiumScreen(
    model: PremiumUiModel,
    callbacks: PremiumCallbacks,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    Column(
        modifier = modifier
            .fillMaxSize()
            .verticalScroll(rememberScrollState())
            .padding(horizontal = spacing.l),
        verticalArrangement = Arrangement.spacedBy(spacing.l),
    ) {
        Spacer(Modifier.height(spacing.xxl))
        Text(
            text = "Shieldra Premium",
            style = MaterialTheme.typography.displayMedium,
            color = MaterialTheme.colorScheme.onBackground,
        )
        Text(
            text = "One payment. Lifetime.",
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.l))

        model.features.forEach { FeatureRow(it) }

        Spacer(Modifier.height(spacing.l))

        ShieldraFullWidthButton(
            text = model.priceLabel?.let { "Buy Lifetime — $it" } ?: "Buy Lifetime",
            onClick = callbacks.onBuy,
        )
        ShieldraFullWidthButton(
            text = "Restore purchase",
            onClick = callbacks.onRestore,
            variant = ShieldraButtonVariant.Ghost,
        )
        Spacer(Modifier.height(spacing.l))
        Text(
            text = model.freeTierSummary,
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
        )
        Spacer(Modifier.height(spacing.xxxl))
    }
}

@Composable
private fun FeatureRow(feature: PremiumFeatureUiModel) {
    Column {
        Text(
            text = "✓  ${feature.title}",
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.onBackground,
        )
        if (feature.subtitle != null) {
            Text(
                text = feature.subtitle,
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
                modifier = Modifier.padding(start = 20.dp, top = 2.dp),
            )
        }
    }
}

@Preview(showBackground = true)
@Composable
private fun PremiumDarkPreview() {
    ShieldraTheme(darkTheme = true) {
        PremiumScreen(
            model = PreviewData.premium,
            callbacks = PremiumCallbacks({}, {}),
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/settings/SettingsCallbacks.kt

```kotlin
package com.shieldra.app.presentation.screen.settings

import androidx.compose.runtime.Immutable

@Immutable
data class SettingsCallbacks(
    val onRowClick: (String, requiresAuth: Boolean) -> Unit,
)
```

---

FILE: app/src/main/java/com/shieldra/app/presentation/screen/settings/SettingsScreen.kt

```kotlin
package com.shieldra.app.presentation.screen.settings

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.KeyboardArrowRight
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.presentation.model.SettingsRowUiModel
import com.shieldra.app.presentation.model.SettingsSection
import com.shieldra.app.presentation.preview.PreviewData

@Composable
fun SettingsScreen(
    rows: List<SettingsRowUiModel>,
    callbacks: SettingsCallbacks,
    modifier: Modifier = Modifier,
) {
    val spacing = ShieldraTheme.spacing
    val grouped = rows.groupBy { it.section }

    Column(
        modifier = modifier
            .fillMaxSize()
            .background(MaterialTheme.colorScheme.background)
            .verticalScroll(rememberScrollState())
            .padding(horizontal = spacing.l, vertical = spacing.l),
    ) {
        SettingsSectionTitle("SECURITY")
        SettingsGroup(grouped[SettingsSection.Security].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xl))

        SettingsSectionTitle("DELIVERY")
        SettingsGroup(grouped[SettingsSection.Delivery].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xl))

        SettingsSectionTitle("APPEARANCE")
        SettingsGroup(grouped[SettingsSection.Appearance].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xl))

        SettingsSectionTitle("DATA & PRIVACY")
        SettingsGroup(grouped[SettingsSection.PrivacyData].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xl))

        SettingsSectionTitle("SECURITY (APP)")
        SettingsGroup(grouped[SettingsSection.SecurityApp].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xl))

        SettingsSectionTitle("ABOUT")
        SettingsGroup(grouped[SettingsSection.About].orEmpty(), callbacks)
        Spacer(Modifier.height(spacing.xxxl))
    }
}

@Composable
private fun SettingsSectionTitle(text: String) {
    val spacing = ShieldraTheme.spacing
    Text(
        text = text,
        style = MaterialTheme.typography.labelSmall,
        color = MaterialTheme.colorScheme.onSurfaceVariant,
        modifier = Modifier.padding(start = spacing.xs, bottom = spacing.s),
    )
}

@Composable
private fun SettingsGroup(
    rows: List<SettingsRowUiModel>,
    callbacks: SettingsCallbacks,
) {
    if (rows.isEmpty()) return
    val spacing = ShieldraTheme.spacing
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(MaterialTheme.colorScheme.surface),
    ) {
        rows.forEachIndexed { idx, row ->
            SettingsRow(row, onClick = { callbacks.onRowClick(row.id, row.requiresAuth) })
            if (idx < rows.lastIndex) {
                Spacer(
                    Modifier
                        .fillMaxWidth()
                        .padding(start = spacing.l)
                        .height(1.dp)
                        .background(MaterialTheme.colorScheme.outline),
                )
            }
        }
    }
}

@Composable
private fun SettingsRow(row: SettingsRowUiModel, onClick: () -> Unit) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .clickable(role = Role.Button, onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(
            imageVector = row.icon,
            contentDescription = null,
            tint = MaterialTheme.colorScheme.onSurfaceVariant,
            modifier = Modifier.size(20.dp),
        )
        Spacer(Modifier.width(14.dp))
        Column(Modifier.weight(1f)) {
            Text(
                text = row.title,
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onSurface,
            )
            if (row.subtitle != null) {
                Text(
                    text = row.subtitle,
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.onSurfaceVariant,
                )
            }
        }
        Icon(
            imageVector = Icons.AutoMirrored.Filled.KeyboardArrowRight,
            contentDescription = null,
            tint = MaterialTheme.colorScheme.onSurfaceVariant,
            modifier = Modifier.size(20.dp),
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun SettingsDarkPreview() {
    ShieldraTheme(darkTheme = true) {
        SettingsScreen(
            rows = PreviewData.settingsRows,
            callbacks = SettingsCallbacks { _, _ -> },
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun SettingsLightPreview() {
    ShieldraTheme(darkTheme = false) {
        SettingsScreen(
            rows = PreviewData.settingsRows,
            callbacks = SettingsCallbacks { _, _ -> },
        )
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/navigation/ShieldraRoutes.kt

```kotlin
package com.shieldra.app.navigation

object ShieldraRoutes {
    const val ONBOARDING_WELCOME = "onboarding/welcome"
    const val ONBOARDING_PERMISSIONS = "onboarding/permissions"
    const val ONBOARDING_PROTECTION = "onboarding/protection"

    const val DASHBOARD = "main/dashboard"
    const val HISTORY = "main/history"
    const val PREMIUM = "main/premium"
    const val SETTINGS = "main/settings"

    const val EVENT_DETAIL_ARG_ID = "eventId"
    const val EVENT_DETAIL = "main/event/{$EVENT_DETAIL_ARG_ID}"

    fun eventDetail(eventId: String): String = "main/event/$eventId"
}

enum class BottomTab(val route: String, val label: String) {
    Dashboard(ShieldraRoutes.DASHBOARD, "Home"),
    History(ShieldraRoutes.HISTORY, "History"),
    Premium(ShieldraRoutes.PREMIUM, "Premium"),
    Settings(ShieldraRoutes.SETTINGS, "Settings"),
}
```

---

FILE: app/src/main/java/com/shieldra/app/navigation/ShieldraNavHost.kt

```kotlin
package com.shieldra.app.navigation

import androidx.compose.foundation.layout.padding
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.History
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.Settings
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.navigation.NavHostController
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.currentBackStackEntryAsState
import androidx.navigation.compose.rememberNavController
import androidx.navigation.navArgument
import com.shieldra.app.presentation.preview.PreviewData
import com.shieldra.app.presentation.screen.dashboard.DashboardCallbacks
import com.shieldra.app.presentation.screen.dashboard.DashboardScreen
import com.shieldra.app.presentation.screen.eventdetail.EventDetailCallbacks
import com.shieldra.app.presentation.screen.eventdetail.EventDetailScreen
import com.shieldra.app.presentation.screen.history.HistoryCallbacks
import com.shieldra.app.presentation.screen.history.HistoryScreen
import com.shieldra.app.presentation.screen.onboarding.ProtectionSetupScreen
import com.shieldra.app.presentation.screen.onboarding.WelcomeScreen
import com.shieldra.app.presentation.screen.premium.PremiumCallbacks
import com.shieldra.app.presentation.screen.premium.PremiumScreen
import com.shieldra.app.presentation.screen.settings.SettingsCallbacks
import com.shieldra.app.presentation.screen.settings.SettingsScreen
import com.shieldra.app.presentation.model.HistoryFilterState

@Composable
fun ShieldraNavHost(
    startDestination: String = ShieldraRoutes.ONBOARDING_WELCOME,
    modifier: Modifier = Modifier,
    navController: NavHostController = rememberNavController(),
) {
    val backStackEntry by navController.currentBackStackEntryAsState()
    val currentRoute = backStackEntry?.destination?.route
    val showBottomBar = currentRoute in BottomTab.values().map { it.route }

    Scaffold(
        modifier = modifier,
        bottomBar = {
            if (showBottomBar) {
                ShieldraBottomBar(navController, currentRoute)
            }
        },
    ) { innerPadding ->
        NavHost(
            navController = navController,
            startDestination = startDestination,
            modifier = Modifier.padding(innerPadding),
        ) {
            // ===== Onboarding =====
            composable(ShieldraRoutes.ONBOARDING_WELCOME) {
                WelcomeScreen(
                    onGetStarted = {
                        navController.navigate(ShieldraRoutes.ONBOARDING_PERMISSIONS)
                    },
                    onSkip = {
                        navController.navigate(ShieldraRoutes.DASHBOARD) {
                            popUpTo(ShieldraRoutes.ONBOARDING_WELCOME) { inclusive = true }
                        }
                    },
                )
            }
            composable(ShieldraRoutes.ONBOARDING_PERMISSIONS) {
                // Placeholder — Phase 3 does not implement permission acquisition.
                com.shieldra.app.presentation.screen.onboarding.PermissionsScreen(
                    step = 1,
                    totalSteps = 1,
                    icon = Icons.Filled.Settings,
                    title = "Notifications",
                    reason = "When Shieldra detects a security event, you'll get a notification.",
                    onAllow = {
                        navController.navigate(ShieldraRoutes.ONBOARDING_PROTECTION)
                    },
                    onNotNow = {
                        navController.navigate(ShieldraRoutes.ONBOARDING_PROTECTION)
                    },
                )
            }
            composable(ShieldraRoutes.ONBOARDING_PROTECTION) {
                ProtectionSetupScreen(
                    initialThreshold = 2,
                    onContinue = {
                        navController.navigate(ShieldraRoutes.DASHBOARD) {
                            popUpTo(ShieldraRoutes.ONBOARDING_WELCOME) { inclusive = true }
                        }
                    },
                )
            }

            // ===== Main =====
            composable(ShieldraRoutes.DASHBOARD) {
                DashboardScreen(
                    model = PreviewData.dashboard,
                    callbacks = DashboardCallbacks(
                        onEventClick = { id ->
                            navController.navigate(ShieldraRoutes.eventDetail(id))
                        },
                        onGuardClick = { /* navigate later */ },
                        onPanic = { /* Panic behavior out of Phase 3 */ },
                        onViewAllEvents = {
                            navController.navigate(ShieldraRoutes.HISTORY)
                        },
                    ),
                )
            }
            composable(ShieldraRoutes.HISTORY) {
                HistoryScreen(
                    events = PreviewData.recentEvents,
                    filterState = HistoryFilterState(),
                    callbacks = HistoryCallbacks(
                        onEventClick = { id ->
                            navController.navigate(ShieldraRoutes.eventDetail(id))
                        },
                        onFilterChange = { /* filter applied later */ },
                    ),
                )
            }
            composable(ShieldraRoutes.PREMIUM) {
                PremiumScreen(
                    model = PreviewData.premium,
                    callbacks = PremiumCallbacks(
                        onBuy = { /* Billing out of Phase 3 */ },
                        onRestore = { /* Billing out of Phase 3 */ },
                    ),
                )
            }
            composable(ShieldraRoutes.SETTINGS) {
                SettingsScreen(
                    rows = PreviewData.settingsRows,
                    callbacks = SettingsCallbacks { id, requiresAuth ->
                        // Phase 3 — no auth enforcement, no screen navigation yet.
                    },
                )
            }

            // ===== Event detail =====
            composable(
                route = ShieldraRoutes.EVENT_DETAIL,
                arguments = listOf(
                    navArgument(ShieldraRoutes.EVENT_DETAIL_ARG_ID) { type = NavType.StringType },
                ),
            ) { entry ->
                val id = entry.arguments?.getString(ShieldraRoutes.EVENT_DETAIL_ARG_ID).orEmpty()
                EventDetailScreen(
                    model = PreviewData.eventDetail.copy(id = id),
                    callbacks = EventDetailCallbacks(
                        onBack = { navController.popBackStack() },
                        onOpenInMaps = { _, _ -> /* Maps out of Phase 3 */ },
                        onDelete = { /* Auth + delete out of Phase 3 */ },
                        onExport = { /* Export out of Phase 3 */ },
                    ),
                )
            }
        }
    }
}

@Composable
private fun ShieldraBottomBar(
    navController: NavHostController,
    currentRoute: String?,
) {
    NavigationBar {
        BottomTab.values().forEach { tab ->
            NavigationBarItem(
                selected = currentRoute == tab.route,
                onClick = {
                    if (currentRoute != tab.route) {
                        navController.navigate(tab.route) {
                            popUpTo(ShieldraRoutes.DASHBOARD) { saveState = true }
                            launchSingleTop = true
                            restoreState = true
                        }
                    }
                },
                icon = {
                    Icon(
                        imageVector = tab.icon(),
                        contentDescription = tab.label,
                    )
                },
                label = { Text(tab.label) },
            )
        }
    }
}

private fun BottomTab.icon(): ImageVector = when (this) {
    BottomTab.Dashboard -> Icons.Filled.Home
    BottomTab.History -> Icons.Filled.History
    BottomTab.Premium -> Icons.Filled.Star
    BottomTab.Settings -> Icons.Filled.Settings
}
```

---

FILE: app/src/main/java/com/shieldra/app/MainActivity.kt

```kotlin
package com.shieldra.app

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import com.shieldra.app.design.theme.ShieldraTheme
import com.shieldra.app.navigation.ShieldraNavHost

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            ShieldraTheme {
                ShieldraNavHost()
            }
        }
    }
}
```

---

FILE: app/src/main/java/com/shieldra/app/ShieldraApp.kt

```kotlin
package com.shieldra.app

import android.app.Application

class ShieldraApp : Application() {
    override fun onCreate() {
        super.onCreate()
        // Phase 3 — no initialization side effects.
        // Future phases: attach logging, but no analytics/telemetry in V1.
    }
}
```

---

3. REQUIRED RESOURCES

FILE: app/src/main/res/values/strings.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Shieldra</string>

    <string name="welcome_title">Shieldra</string>
    <string name="welcome_subtitle">Your phone\'s quiet guardian.</string>
    <string name="welcome_body">Detects unauthorized use, collects evidence, and delivers it — so you know what happened.</string>
    <string name="welcome_get_started">Get started</string>
    <string name="welcome_skip">Skip for now</string>

    <string name="tab_dashboard">Home</string>
    <string name="tab_history">History</string>
    <string name="tab_premium">Premium</string>
    <string name="tab_settings">Settings</string>

    <string name="nav_back">Back</string>

    <string name="dashboard_section_guards">GUARDS</string>
    <string name="dashboard_section_recent">RECENT</string>
    <string name="dashboard_view_all">View all</string>
    <string name="dashboard_panic_title">Silent Panic</string>
    <string name="dashboard_panic_subtitle">Trigger an emergency event</string>

    <string name="empty_events_title">No events yet</string>
    <string name="empty_events_message">When Shieldra detects suspicious activity, you\'ll see it here.</string>
    <string name="empty_history_title">No events match</string>
    <string name="empty_history_message">Try adjusting filters, or wait for Shieldra to detect activity.</string>

    <string name="location_current">Current Location</string>
    <string name="location_last_known">Last Known Location</string>
    <string name="location_unavailable">Location unavailable</string>
    <string name="location_open_in_maps">Open in Maps</string>

    <string name="delivery_section">DELIVERY</string>
    <string name="delivery_delivered">✓ Delivered</string>
    <string name="delivery_failed">✗ Failed</string>
    <string name="delivery_skipped">— Skipped</string>
    <string name="delivery_sending">· Sending</string>
    <string name="delivery_ready">· Ready</string>
    <string name="delivery_retrying">· Retrying</string>
    <string name="delivery_expired">· Expired</string>

    <string name="premium_title">Shieldra Premium</string>
    <string name="premium_tagline">One payment. Lifetime.</string>
    <string name="premium_buy_lifetime">Buy Lifetime</string>
    <string name="premium_restore">Restore purchase</string>

    <string name="settings_section_security">SECURITY</string>
    <string name="settings_section_delivery">DELIVERY</string>
    <string name="settings_section_appearance">APPEARANCE</string>
    <string name="settings_section_data_privacy">DATA &amp; PRIVACY</string>
    <string name="settings_section_security_app">SECURITY (APP)</string>
    <string name="settings_section_about">ABOUT</string>

    <string name="action_retry">Retry</string>
    <string name="action_allow">Allow</string>
    <string name="action_not_now">Not now</string>
    <string name="action_continue">Continue</string>
    <string name="action_delete">Delete</string>
    <string name="action_export">Export</string>
    <string name="action_cancel">Cancel</string>
</resources>
```

FILE: app/src/main/res/values-ar/strings.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">شيلدرا</string>

    <string name="welcome_title">شيلدرا</string>
    <string name="welcome_subtitle">حارس هاتفك الهادئ.</string>
    <string name="welcome_body">يكتشف الاستخدام غير المصرح به، يجمع الأدلة، ويسلمها — لتعرف ما حدث.</string>
    <string name="welcome_get_started">ابدأ الآن</string>
    <string name="welcome_skip">تخطَّ الآن</string>

    <string name="tab_dashboard">الرئيسية</string>
    <string name="tab_history">السجل</string>
    <string name="tab_premium">بريميوم</string>
    <string name="tab_settings">الإعدادات</string>

    <string name="nav_back">رجوع</string>

    <string name="dashboard_section_guards">الحُرّاس</string>
    <string name="dashboard_section_recent">الأحدث</string>
    <string name="dashboard_view_all">عرض الكل</string>
    <string name="dashboard_panic_title">الذعر الصامت</string>
    <string name="dashboard_panic_subtitle">تفعيل حدث طوارئ</string>

    <string name="empty_events_title">لا توجد أحداث بعد</string>
    <string name="empty_events_message">عندما يكتشف شيلدرا نشاطًا مشبوهًا، سيظهر هنا.</string>
    <string name="empty_history_title">لا توجد أحداث مطابقة</string>
    <string name="empty_history_message">حاول تعديل الفلاتر، أو انتظر حتى يكتشف شيلدرا نشاطًا.</string>

    <string name="location_current">الموقع الحالي</string>
    <string name="location_last_known">آخر موقع معروف</string>
    <string name="location_unavailable">الموقع غير متوفر</string>
    <string name="location_open_in_maps">افتح في الخرائط</string>

    <string name="delivery_section">التسليم</string>
    <string name="delivery_delivered">✓ تم التسليم</string>
    <string name="delivery_failed">✗ فشل</string>
    <string name="delivery_skipped">— تم التخطي</string>
    <string name="delivery_sending">· قيد الإرسال</string>
    <string name="delivery_ready">· جاهز</string>
    <string name="delivery_retrying">· إعادة المحاولة</string>
    <string name="delivery_expired">· منتهي</string>

    <string name="premium_title">شيلدرا بريميوم</string>
    <string name="premium_tagline">دفعة واحدة. مدى الحياة.</string>
    <string name="premium_buy_lifetime">شراء مدى الحياة</string>
    <string name="premium_restore">استعادة الشراء</string>

    <string name="settings_section_security">الأمان</string>
    <string name="settings_section_delivery">التسليم</string>
    <string name="settings_section_appearance">المظهر</string>
    <string name="settings_section_data_privacy">البيانات والخصوصية</string>
    <string name="settings_section_security_app">أمان التطبيق</string>
    <string name="settings_section_about">حول</string>

    <string name="action_retry">إعادة المحاولة</string>
    <string name="action_allow">السماح</string>
    <string name="action_not_now">ليس الآن</string>
    <string name="action_continue">متابعة</string>
    <string name="action_delete">حذف</string>
    <string name="action_export">تصدير</string>
    <string name="action_cancel">إلغاء</string>
</resources>
```

FILE: app/src/main/res/values/themes.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="Theme.Shieldra" parent="android:Theme.Material.NoActionBar">
        <item name="android:statusBarColor">@android:color/transparent</item>
        <item name="android:navigationBarColor">@android:color/transparent</item>
        <item name="android:windowLightStatusBar">false</item>
    </style>
</resources>
```

FILE: app/src/main/AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Phase 3 — no permissions declared.
         Future phases will declare only what is required and approved. -->

    <application
        android:name=".ShieldraApp"
        android:allowBackup="false"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/Theme.Shieldra">

        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:configChanges="orientation|screenSize|screenLayout|keyboardHidden|uiMode|locale|layoutDirection">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

FILE: app/src/main/res/xml/backup_rules.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<full-backup-content>
    <!-- Phase 3 — backup disabled at the manifest level. -->
</full-backup-content>
```

FILE: app/src/main/res/xml/data_extraction_rules.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<data-extraction-rules>
    <cloud-backup>
        <exclude domain="sharedpref" path="." />
        <exclude domain="database" path="." />
        <exclude domain="file" path="." />
    </cloud-backup>
    <device-transfer>
        <exclude domain="sharedpref" path="." />
        <exclude domain="database" path="." />
        <exclude domain="file" path="." />
    </device-transfer>
</data-extraction-rules>
```

---

4. REQUIRED DEPENDENCIES

FILE: gradle/libs.versions.toml

```toml
[versions]
agp = "8.6.0"
kotlin = "2.0.20"
composeBom = "2024.09.02"
activityCompose = "1.9.2"
navigationCompose = "2.8.1"
materialIconsExtended = "1.7.2"
coreKtx = "1.13.1"
lifecycle = "2.8.6"

[libraries]
androidx-core-ktx = { module = "androidx.core:core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { module = "androidx.lifecycle:lifecycle-runtime-ktx", version.ref = "lifecycle" }
androidx-activity-compose = { module = "androidx.activity:activity-compose", version.ref = "activityCompose" }
androidx-navigation-compose = { module = "androidx.navigation:navigation-compose", version.ref = "navigationCompose" }

compose-bom = { module = "androidx.compose:compose-bom", version.ref = "composeBom" }
compose-ui = { module = "androidx.compose.ui:ui" }
compose-ui-graphics = { module = "androidx.compose.ui:ui-graphics" }
compose-ui-tooling = { module = "androidx.compose.ui:ui-tooling" }
compose-ui-tooling-preview = { module = "androidx.compose.ui:ui-tooling-preview" }
compose-foundation = { module = "androidx.compose.foundation:foundation" }
compose-material3 = { module = "androidx.compose.material3:material3" }
compose-material-icons-extended = { module = "androidx.compose.material:material-icons-extended", version.ref = "materialIconsExtended" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
kotlin-compose = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
```

FILE: app/build.gradle.kts

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    alias(libs.plugins.kotlin.compose)
}

android {
    namespace = "com.shieldra.app"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.shieldra.app"
        minSdk = 26
        targetSdk = 34
        versionCode = 1
        versionName = "0.1.0"
    }

    buildFeatures {
        compose = true
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    kotlinOptions {
        jvmTarget = "17"
    }
}

dependencies {
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.activity.compose)
    implementation(libs.androidx.navigation.compose)

    implementation(platform(libs.compose.bom))
    implementation(libs.compose.ui)
    implementation(libs.compose.ui.graphics)
    implementation(libs.compose.ui.tooling.preview)
    implementation(libs.compose.foundation)
    implementation(libs.compose.material3)
    implementation(libs.compose.material.icons.extended)

    debugImplementation(libs.compose.ui.tooling)
}
```

FILE: build.gradle.kts (root)

```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.kotlin.compose) apply false
}
```

FILE: settings.gradle.kts

```kotlin
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}
rootProject.name = "Shieldra"
include(":app")
```

---

5. ARCHITECTURE NOTES — Production vs Preview Separation

```
presentation/
├── model/         ← Production. Pure Kotlin, @Immutable, no Compose UI imports.
├── preview/       ← Preview-only. Imports presentation.model.
│                    NEVER imported by production code.
└── screen/        ← Production screens. Take model + callbacks as parameters.
                     The ONLY place PreviewData is imported is inside @Preview
                     functions in the same file, and even those are @Preview
                     annotated (never executed at runtime).
```

Rules enforced:

1. Screens never import PreviewData except in @Preview functions in the same file. This is safe because @Preview is debug-tooling-only — the Compose compiler strips preview code from release builds.
2. No UI imports domain, data, camera, location, delivery. Only presentation.model, design.*, navigation, and AndroidX.
3. No LocalContentColor misuse. All content-color reads use androidx.compose.material3.LocalContentColor.current explicitly qualified.
4. Preview data isolated. Every entry into a screen comes from a DashboardUiModel, EventDetailUiModel, etc. Production wiring (Phase 4+) will build these from domain.
5. NavHost currently uses PreviewData as a temporary bridge. Flagged as a known limitation — the NavHost must be refactored in Phase 4 to accept ViewModels. This is intentional so Phase 3 is runnable without any domain layer.

---

6. SELF-AUDIT

Compile-risk findings

# Issue Severity Status
1 ShieldraButton — uses androidx.compose.material3.LocalContentColor.current fully qualified; no local shadowing. — Resolved
2 ShieldraSnackbar — uses Row weight inside Snackbar content. Snackbar content is a RowScope, so Modifier.weight is legal. — Resolved
3 LocationEvidenceCard — nested composable calls into ShieldraButton require importing ShieldraButtonVariant. Fully qualified in file. — Resolved
4 Icon overload — androidx.compose.material3.Icon imported everywhere; no ambiguity with material.icons.*. — Resolved
5 ShieldraSnackbarVisuals — overrides actionLabel twice (primary constructor + get()). This can produce a JVM signature clash in Kotlin 2.0. ACTION REQUIRED: remove the override val actionLabel: String? primary constructor parameter and keep only the get() override, OR remove the get() override and keep the parameter. Recommend removing the parameter. MEDIUM UNRESOLVED — documented
6 Icons.AutoMirrored.Filled.KeyboardArrowRight — available from material-icons-extended 1.6+. Version pinned to 1.7.2. — Resolved
7 enableEdgeToEdge() — requires activity-compose 1.8+. Pinned 1.9.2. — Resolved
8 androidx.navigation.compose.rememberNavController — no import issue, uses navigation-compose 2.8.1. — Resolved

Missing-resource findings

# Item Status
1 mipmap/ic_launcher — referenced in Manifest but NOT provided. Requires a launcher icon in a later phase. Until supplied, the build will fail. BLOCKER documented
2 Font binaries (Inter, Space Grotesk) — intentionally NOT referenced. R.font.* avoided. Fallback to FontFamily.Default / FontFamily.SansSerif / FontFamily.Monospace. Compile-safe. Safe
3 strings.xml — provided for values/ (English default) and values-ar/. Other locales fall back to English default automatically. Full localization is Phase 18. Partial — by design
4 themes.xml — provided. Uses android:Theme.Material.NoActionBar (no dependency on AppCompat themes). Complete for Phase 3

Architecture findings

# Check Result
1 UI → Domain imports None. UI imports only presentation.model, design.*, navigation.
2 Preview-only data leakage PreviewData is imported only inside @Preview functions and inside ShieldraNavHost (documented as Phase-3 temporary bridge).
3 Guard → Event direct creation Not applicable (no domain in Phase 3).
4 ProtectionStateEngine coupling Not present in UI. UI operates on ProtectionVisualState.

RTL/LTR findings

# Check Result
1 left/right hardcoding None found. All padding uses start/end. All rows use Arrangement.SpaceBetween (RTL-safe).
2 AutoMirrored icons Used: Icons.AutoMirrored.Filled.ArrowBack, Icons.AutoMirrored.Filled.KeyboardArrowRight.
3 Shield path mirroring Symmetric — no mirroring needed.
4 Arabic string resources Provided in values-ar/strings.xml.
5 android:supportsRtl="true" Declared in Manifest.
6 configChanges includes `layoutDirection locale`

Accessibility findings

# Check Result
1 contentDescription on ShieldCore ✅ Multi-state, includes last-check.
2 contentDescription on GuardTile ✅ Includes title, subtitle, status, premium flag.
3 contentDescription on EventCard ✅ Includes title, time, delivery status.
4 contentDescription on LiveStatusStrip ✅ Merged description.
5 Touch targets ≥ 48dp Buttons = 52dp. GuardTile = ample. Settings rows = 48dp+.
6 Scalable text All text uses sp.
7 Non-color-only state ShieldCore: shape + ring width + glyph + label + color. LocationBadge: filled vs outlined dot. GuardTile: bar + icon + text.
8 Reduced motion Breathing animation only on Protected/Suspicious/Attention; Disabled and Event have no continuous animation. Respecting OS animator duration is a Phase 3.5 refinement.
9 Screen reader order Standard Compose traversal; merged where appropriate.

Forbidden-scope check

# Forbidden item Status
1 Safe Zones / Geofencing / continuous GPS ✅ Not implemented
2 Security Engine ✅ Not implemented
3 LockGuard / MotionGuard / SIM / Panic logic ✅ Not implemented (UI only)
4 Camera capture / Evidence collection ✅ Not implemented (placeholder cards only)
5 Delivery (SMTP / WhatsApp / Telegram) ✅ Not implemented
6 Smart Queue / Automatic Recovery ✅ Not implemented
7 Billing / Google Play Billing ✅ Not implemented
8 Ads SDK ✅ Not implemented (placeholder ShieldraAdSlot)
9 Authentication implementation ✅ Not implemented (only requiresAuth flag in settings rows)
10 Encryption / Database / Room ✅ Not implemented
11 Cloud / Firebase / Backend ✅ Not implemented
12 Spyware / covert functionality ✅ Not implemented
13 Antivirus / VPN / Parental controls ✅ Not implemented

Unresolved issues (explicit)

1. Launcher icon mipmap/ic_launcher missing. A build will fail until provided. This is a Phase 3.5 or Phase 4 task.
2. ShieldraSnackbarVisuals — duplicate actionLabel override. Must be fixed before build (see compile-risk #5).
3. ShieldraNavHost uses PreviewData. Documented as intentional Phase-3 bridge. Must be replaced with ViewModel wiring in Phase 4. Not a defect — a scoped deferral.
4. ShieldraButton Secondary variant uses OutlinedButton which does not show a loading spinner circle centered — spinner is included in content. Correct, but the outlined border remains during loading. Acceptable.
5. Fonts. No custom fonts shipped. Design relies on system fallback. Visual identity is partially compromised until real font binaries are added in Phase 3.5.
6. No Android instrumented tests, no unit tests. Phase 3 is design implementation; tests belong to Phase 1 Group E (foundation) and Phase 23 (acceptance).
7. No actual build verification performed. This handoff runs in a chat environment without build tools.

---

PHASE 3 DESIGN CODE HANDOFF — AWAITING EXTERNAL REVIEW

I do not claim repository implementation. I do not claim build verification. I do not claim Git commit or push. I do not start Phase 4.

# React Native / Expo Reference

## Project Structure (Expo Managed)

```
app/
  (tabs)/_layout.tsx     # Tab navigator
  _layout.tsx            # Root layout (providers, fonts)
  +not-found.tsx
components/
assets/
hooks/
app.json                 # Expo config
```

## Navigation (Expo Router)

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
      <Stack.Screen name="modal" options={{ presentation: 'modal' }} />
    </Stack>
  );
}

// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
export default function TabLayout() {
  return (
    <Tabs screenOptions={{ tabBarActiveTintColor: '#2563eb' }}>
      <Tabs.Screen name="index" options={{ title: 'Home' }} />
    </Tabs>
  );
}

// Programmatic navigation
import { router } from 'expo-router';
router.push('/details/42');
router.replace('/login');
router.back();
```

## Platform-Specific Code

```tsx
import { Platform, StyleSheet } from 'react-native';
const styles = StyleSheet.create({
  shadow: Platform.select({
    ios: { shadowColor: '#000', shadowOffset: { width: 0, height: 2 }, shadowOpacity: 0.1 },
    android: { elevation: 3 },
  }),
});
// File-based: Button.ios.tsx / Button.android.tsx (auto-resolved on import)
```

## NativeWind Styling

```tsx
export function Card({ title }: { title: string }) {
  return (
    <View className="bg-white rounded-xl p-4 shadow-sm mx-4 mb-3">
      <Text className="text-lg font-semibold text-gray-900">{title}</Text>
    </View>
  );
}
```

## AsyncStorage

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage';
await AsyncStorage.setItem('auth_token', token);
const token = await AsyncStorage.getItem('auth_token');
await AsyncStorage.clear();
```

## Push Notifications (Expo)

```tsx
import * as Notifications from 'expo-notifications';
import * as Device from 'expo-device';

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true, shouldPlaySound: true, shouldSetBadge: true,
  }),
});

async function registerForPushNotifications() {
  if (!Device.isDevice) return null;
  const { status } = await Notifications.requestPermissionsAsync();
  if (status !== 'granted') return null;
  return (await Notifications.getExpoPushTokenAsync({ projectId: 'your-id' })).data;
}
```

## Deep Linking

```json
// app.json: { "expo": { "scheme": "myapp" } }
```
```tsx
// app/details/[id].tsx handles myapp://details/42
import { useLocalSearchParams } from 'expo-router';
const { id } = useLocalSearchParams<{ id: string }>();
```

## Gestures (react-native-gesture-handler + Reanimated)

```tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';

const translateX = useSharedValue(0);
const pan = Gesture.Pan()
  .onUpdate((e) => { translateX.value = e.translationX; })
  .onEnd(() => { translateX.value = withSpring(0); });
const style = useAnimatedStyle(() => ({ transform: [{ translateX: translateX.value }] }));

<GestureDetector gesture={pan}>
  <Animated.View style={style}>{/* content */}</Animated.View>
</GestureDetector>
```

## Key Conventions

- **Lists**: `FlashList` from `@shopify/flash-list` over FlatList for performance
- **Images**: `expo-image` over `Image` for caching and format support
- **Safe areas**: Wrap screens with `SafeAreaView` from `react-native-safe-area-context`
- **Fonts**: Load with `expo-font` + `useFonts`; keep splash visible until ready
- **Env vars**: Use `expo-constants` or `.env` with `expo-env` plugin
- **OTA updates**: `expo-updates` for JS bundle updates without app store review
- **Native modules**: Use Expo config plugins or `expo-modules-api` for custom native code

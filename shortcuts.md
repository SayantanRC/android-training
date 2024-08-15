# Shortcuts

## Get service
Old:
```
val notificationManager =
    context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
```
New:
```
val notificationManager = context.getSystemService<NotificationManager>()
```

## Write to `SharedPreference`
Old:
```
val editor = sharedPref.edit()
editor.run {
    putString(MY_KEY, value)
}
editor.apply()
```
New:
```
sharedPref.edit(commit = true) {
    putString(MY_KEY, value)
}
```

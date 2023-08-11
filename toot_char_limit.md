# Zvýšení počtu znaků per toot

Postup z [How to increase the character limit for toots in Mastodon](https://draklyckan.se/how-to-increase-the-character-limit-for-toots-in-mastodon/)

## 1 Kroky:

1. `su - mastodon`
2. `nano live/app/javascript/mastodon/features/compose/components/compose_form.js` - najít výskyty řetězce `500` a změnit na 2500 (2x)
3. `nano live/app/validators/status_length_validator.rb` - dtto (1x)
4. `nano live/app/serializers/rest/instance_serializer.rb` - za `:registrations` dopsat `, :max_toot_chars`
5. V témže souboru **nad** řádek `private` vložit:

```
def max_toot_chars 
   2500
end
```

## 2 Překompilovat: 
```
cd live 
RAILS_ENV=production bundle exec rails assets:precompile
```

## 3 Restartovat
```
systemctl restart mastodon-sidekiq
systemctl reload mastodon-web
```

## Troubleshooting

### Pokud se web nenahrává a držkuje kvůli integrity checksum (bílá obrazovka atd), tak:
```
su - mastodon
nano live/public/packs/manifest.json
```

Pak změnit checksums podle toho, co píše prohlížeč, reload mastodon-web.

[zdroj](https://github.com/mastodon/mastodon/pull/15096)

### Pokud precompile hází chyby "Unexpected token"
```
su - mastodon
cd live
git checkout yarn.lock
```

[zdroj](https://github.com/mastodon/mastodon/issues/25741#issuecomment-1628521683)

### Pokud precompile nehodlá doběhnout (out of memory)

```
nano live/config/webpack/production.js
```
změnit `devtool: 'source-map'` na `devtool: 'none'`
změnit `parallel: true` na false
změnit `sourceMap: true` na false


[zdroj](https://www.mattburkedev.com/adventures-in-mastodon-self-hosting-upgrade-to-4-1-0rc1/)
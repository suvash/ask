# Setup experimental features

The experimental features for nix can be enabled either per invocation or by setting a user specific configuration.

For individual invocation, one specifies what expermental features to be used, for eg. to enable the `nix-command` in this case
```bash
nix --experimental-features nix-command build ...
```

When setting a user specific configuration
```nix
# ~/.config/nix/nix.conf
experimental-features = nix-command
```

On NixOS, this can be set directly as a nix setting 
```nix
# configuration.nix
{ pkgs, ... }: {
  …
  nix.settings.experimental-features = [ "nix-command" ];
  …
}
```

## Enable nix-command and flakes for NixOS
```nix
# configuration.nix
{ pkgs, ... }: {
  …
  nix.settings.experimental-features = [ "nix-command" "flakes" ];
  …
}
```


## Source
- https://nixos.wiki/wiki/Nix_command
- https://discourse.nixos.org/t/using-experimental-nix-features-in-nixos-and-when-they-will-land-in-stable/7401/3

---
description: Moving to Fedora Sway Atomic brings many security benefits, but it also
  introduces some multimedia challenges. One of the first issues I encountered was
  video playback—the pre-installed Firefox couldn't play videos from various sources,
  including my Jellyfin media server. Here's how I solved it completely.
pubDate: '2025-06-13'
title: 'Fixing Video Playback in Fedora Sway Atomic: A Complete Guide'
---


Moving to Fedora Sway Atomic brings many security benefits, but it also introduces some multimedia challenges. One of the first issues I encountered was video playback—the pre-installed Firefox couldn't play videos from various sources, including my Jellyfin media server. Here's how I solved it completely.

## The Problem

Fresh Fedora Sway Atomic installations come with a minimal Firefox that lacks many codecs needed for modern web video playback. Videos from streaming services, self-hosted media servers like Jellyfin, and even some YouTube content simply wouldn't play. This is due to patent and licensing restrictions that prevent Fedora from shipping with certain multimedia codecs by default.

## The Solution: RPM Fusion + Flatpak Firefox

After trying several approaches, the solution that worked reliably involved combining RPM Fusion repositories for codec support with a Flatpak Firefox browser. Here's the complete step-by-step process:

### Step 1: Download RPM Fusion Repository Files

First, download the RPM Fusion repository configuration files:

```bash
wget https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-42.noarch.rpm
wget https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-42.noarch.rpm
```

### Step 2: Install RPM Fusion Repositories

Install both the free and non-free RPM Fusion repositories using rpm-ostree:

```bash
sudo rpm-ostree install ./rpmfusion-free-release-42.noarch.rpm ./rpmfusion-nonfree-release-42.noarch.rpm
```

This command adds the RPM Fusion repositories to your system, enabling access to additional codecs and multimedia packages.

### Step 3: Reboot to Apply Repository Changes

```bash
systemctl reboot
```

The reboot is necessary for the new repositories to become active in the rpm-ostree system.

### Step 4: Replace System FFmpeg with RPM Fusion Version

Once the system reboots, replace the default FFmpeg with the enhanced version from RPM Fusion:

```bash
sudo rpm-ostree override replace --experimental --from repo=rpmfusion-free-updates ffmpeg
```

This command uses the experimental override feature to replace the system FFmpeg package with a version that includes additional codec support.

### Step 5: Reboot Again

```bash
systemctl reboot
```

Another reboot is required to apply the FFmpeg replacement.

### Step 6: Install Flatpak Firefox

Finally, install Firefox from Flathub, which comes with better codec support:

```bash
flatpak install flathub org.mozilla.firefox
```

The Flatpak version of Firefox includes additional multimedia support and isn't subject to the same codec restrictions as the system Firefox.

## Why This Approach Works

This solution works because it addresses the codec issue at multiple levels:

1. **RPM Fusion repositories** provide access to additional multimedia codecs that aren't available in the default Fedora repositories
2. **FFmpeg replacement** ensures the system has comprehensive multimedia framework support
3. **Flatpak Firefox** comes with broader codec support and proper sandboxing

## Alternative Approaches That Didn't Work

Before finding this solution, I tried several other approaches that were unsuccessful:

- Installing codecs directly with `rpm-ostree install` (limited codec availability)
- Using only the system Firefox with codec additions (missing dependencies)
- Installing media codecs as Flatpaks (incomplete integration)

## The Result

After completing these steps, video playback works seamlessly across:
- Jellyfin media server
- YouTube (all formats)
- Streaming services
- Local media files
- Embedded videos on websites

## Important Considerations

### Security Implications

While this solution enables full multimedia support, it's worth noting that:
- RPM Fusion packages aren't maintained by Fedora directly
- The `--experimental` flag indicates this is a newer rpm-ostree feature
- Flatpak Firefox runs in a sandbox, maintaining security isolation

### System Integrity

The beauty of rpm-ostree is that if anything goes wrong, you can easily rollback:

```bash
# Check your deployment history
rpm-ostree status

# Rollback to previous deployment if needed
rpm-ostree rollback
```

## Conclusion

Fedora Sway Atomic's immutable approach requires thinking differently about multimedia support, but the solution is straightforward once you understand the layered approach. By combining RPM Fusion repositories for codec access, system-level FFmpeg replacement, and a properly equipped Flatpak Firefox, you get complete video playback support while maintaining the security benefits of the immutable system.

This experience reinforces why Fedora Sway Atomic's architecture is so powerful—even when you need to make significant multimedia changes, the atomic nature means you can experiment safely, knowing you can always roll back if something doesn't work as expected.

# Installing Komenti

You can install Komenti from the package, and then add it to your execution path like so:

```bash
cd ~
wget https://github.com/reality/komenti/releases/download/0.2.0-SNAPSHOT-5/komenti-0.2.0-SNAPSHOT.zip
unzip komenti-0.2.0-SNAPSHOT.zip
echo 'export PATH=$PATH:~/komenti-0.2.0-SNAPSHOT/bin' >> ~/.bashrc
source ~/.bashrc
```

It should then be available as the komenti command in your current terminal emulator, and future emulators in the same environment.

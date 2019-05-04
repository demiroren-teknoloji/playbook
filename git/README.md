# Git

## Konfigürasyon Temelleri

Proje bazında mutlaka şirket **e-posta adresinizi** kullanın:

```bash
git config user.name "Ad Soyad"
git config user.email "ad.soyad@demirorenteknoloji.com"
```

## Commit ve GNUPGP İmza

Commit için template kullanın. Örnek dosya: `~/.git-commit-template`

```bash

#
# 3456789x123456789x123456789x123456789x123456789x
# Kısa açıklama (konu) : 50 karakter

# 3456789x123456789x123456789x123456789x123456789x123456789x123456789x12
# Uzun açıklama : 72 karakter
#
# - Bu değişiklik neden önemli?
# - Problemi nasıl çözdü?
# - Herhangi bir yan etkisi var mı?
#
# Fixes #ticket
# Closes #ticket, #ticket, #ticket
#
# Eğer varsa GitHub, GitLab, BitBucket ya da JIRA ticket/issue linkleri
#
```

Bu template’ı her commit esnasında kullanmak için;

```bash
git config --global commit.template "~/.git-commit-template"
```

şeklinde ayarlara ekleyebilirsiniz. Eğer bu template çıkmadan hızlıca kısa
açıklamayı yapıp ilerlemek isterseniz; `git commit -m 'Add XYZ feature'`
şeklinde kullanabilirsiniz.

Eğer template’ın görüntülendiği anda, ek olarak değişiklikleri de `diff`
şeklinde görmek isterseniz;

```bash
git config --global commit.verbose true
```

yapmanız gerekir.

Yaptığınız bütün commit’leri GNUPGP ile imzalamanız önerilir. Bu sayede gerçekten
commit’in sizin tarafınızdan yapıldığı garantilenmiş olur.

macOS kullanıcıları, [Homebrew][homebrew] üzerinden gerekli kurulumu yapabilir:

```bash
brew install gnupg pinentry-mac
```

Daha sonra;

```bash
gpg --gen-key

Real name: Ad Soyad
Email address: ad.soyad@demirorenteknoloji.com 
You selected this USER-ID:                    
    "Ad Soyad <ad.soyad@demirorenteknoloji.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? O

# şimdi en az sekiz karakterli özel şifrenizi girin

gpg: key 864926852796551C marked as ultimately trusted
gpg: revocation certificate stored as '/Users/vigo/.gnupg/openpgp-revocs.d/0DEF20B7D3CEE2F3D9ACC7B3864926852796551C.rev'
public and secret key created and signed.

pub   rsa2048 2019-05-04 [SC] [expires: 2021-05-03]
      0DEF20B7D3CEE2F3D9ACC7B3864926852796551C
uid                      Ad Soyad <ad.soyad@demirorenteknoloji.com>
sub   rsa2048 2019-05-04 [E] [expires: 2021-05-03]
```

şimdi anahtarları listeleyin:

```bash
gpg --list-secret-keys

sec   rsa2048 2019-05-04 [SC] [expires: 2021-05-03]
      0DEF20B7D3CEE2F3D9ACC7B3864926852796551C               # <--- Bu signkey
uid           [ultimate] Ad Soyad <ad.soyad@demirorenteknoloji.com>
ssb   rsa2048 2019-05-04 [E] [expires: 2021-05-03]
```

Şimdi git ayarlarına ek yapın;

```bash
git config commit.gpgsign true
git config user.signingkey 0DEF20B7D3CEE2F3D9ACC7B3864926852796551C
git config gpg.program gpg
```

Şimdi bu anahtarı (*public key’i*) GitHub’a aktarmak için;

```bash
gpg --armor --export 0DEF20B7D3CEE2F3D9ACC7B3864926852796551C
```

çıktıyı;

```bash
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQENBFzN5RABCADUfjK7pE3beLEW0FJoO/bT7W5nrUGDTqxRLZQWefl1Wie1TjQa
zbe2v5Fsr5q8v+QgD00GhkA46qxEDJ6BbDPPQrfN3DEZazgWD4RJzBCeHqYmEbIW
wEJFOJX3G1BomzwSDh/TQJmcfBVCvAF1QxRR4Lc1qd53rH+nuArsCyAWWYjMHB+Z
2gfqg8ZpT2QlEFvj6M2itZWm+nRaJRg78EMJ9iyhbV0x5+w0miOuNM5DgJTaJ5gr
RiI1cAXxNHlVQ6X7PoZmUfIprt8UkJckvXD5fMP6rFiIh5XefM6OsIKck116Pj0q
e9KOtoBJ/oT7Qhl6zT9ORF8tBa3DIpULMZ6LABEBAAG0KkFkIFNveWFkIDxhZC5z
b3lhZEBkZW1pcm9yZW50ZWtub2xvamkuY29tPokBVAQTAQgAPhYhBA3vILfTzuLz
2azHs4ZJJoUnllUcBQJczeUQAhsDBQkDwmcABQsJCAcCBhUKCQgLAgQWAgMBAh4B
AheAAAoJEIZJJoUnllUcGfoH/RLp7nMXeACuNHp4Y6Loyt9T5gESHZIgldm+SW0L
BIObtL08R90NvS3rNVHfJ0m6ZBk62b7ik4ngulyDT6Ze6RveCWDOv24yseP9PIzx
0qvm8oWR5XKjn4CfiFvCYgoko8pRuNc2f0QPz2AUdFZM7TGvyscMjdorHCOnl8iJ
fKyw1IgDAGLw6A682/qQU2pTNcGgtNLkgdPvW0YWY+0PHq8ckXfaoYMmRNGNIYZZ
PieL+4Zb2Q5J20Ep2fitLf0PrF1EvNSPpMcDlhsIfByICjNSg79MlYY/ob1EoquG
hn5Rnn0Typy0ZB+cq9xJ819jtDIMhIHHt5cdAvSsAX31JYi5AQ0EXM3lEAEIALac
kuGXcGg6BR448xZh7llprwpNXzG1/OEbmN2d38VFDikamDWQqZPSJKcYd+PIjZZJ
VwZat+OF+Seo+J46RbQbso/jqJ6+1zZuEWcjswofQ0rEnhOac3lGlZk7vy3+VlbJ
sY+Db0rXuwUZ9Xh8peTptTQfRleQ9Z5VoPU61cCosP/4/BtnHJo0zYIEzN2e9Jd1
HE5/LgxpVdhN/5Y2ZxQQH6XhJZPzGbilQPoK/nU7l/PX/PB8v6xiAl4cod0NU76x
RTZP1VYG3le9WI6FBpzxHz6Qb7FlPy96B4grxf9k++LZqsKPX6RxkkcTXKszN4GI
MUn11oMk+WNyDPwr/IUAEQEAAYkBPAQYAQgAJhYhBA3vILfTzuLz2azHs4ZJJoUn
llUcBQJczeUQAhsMBQkDwmcAAAoJEIZJJoUnllUcPqkH/3oumDnlQj2q0JCtUDWX
G0ugY3yT+/IF+I8uuNvw++38FYlKhbZFWcpm7Tgk9/uPmc5DVTupXbtfIMCjf2tc
+W4k6C0J/St92Jn3zkH/aF2MVilhHy/TMdFroC7Xfuv78TC9974/brrOn0Rkj3nY
8E3DRlCKwqdThjFVoy729v7GV/5yNNje4LuzZL1jnnno3+TbJcpEUhLdFcDNVR1p
0g2X/lWiIC01jQmf+grNho5siPspnVd/jkZ6ZHzeaBQP/Z118MZq5l4UkWml2EL2
xQzV0+ZI1hiqojuzn+G/q40OOoDIliI27LWhLrAp/kshSO80CZuZomm2ZoROXkp0
rbI=
=TXjq
-----END PGP PUBLIC KEY BLOCK-----
```

komple kopyalayıp; https://github.com/settings/keys sayfasından, **GPG keys**’e
eklemeniz gerekmektedir. Bu işlemler sonucunda artık yaptığınız her commit
size özel imzalanmış olup, GitHub ve benzeri servislerde **verified** olarak
görüntülenecektir.

## Commit Mesajı Nasıl Yazılır?

Commit mesajının ilk satırı **subject** (konu) yani kısa açıklama satırıdır.
50 karakterden fazla mesaj yazılmaz. Detaylı açıklama, bir boş satır verildikten
sonra yazılır. Bu kısımda herhangi bir limit yoktur...

Mutlaka geniş zaman kullanılmalıdır. Çünkü commit mesajı geniş zamanı ifade
eder.

`Added XYZ feature` yerine `Add XYZ feature` şeklinde yazılmalıdır. Eğer mesajları
Türkçe yazıyorsak; `XYZ özelliği eklendi` yerine `XYZ özelliği ekle` şeklinde
yazmak daha uygundur.

Kısa açıklamalar `git log --oneline` durumunda işe yarar. Uzun açıklama kısmı
`git log` ile bakıldığında görünür.

Eğer GitHub Issue servisini kullanıyorsanız, commit mesajı içinde;

- `fixes #11`
- `closes #11`
- `resolves #11`

gibi anahtar kelimeler kullanarak otomatik olarak issue’yu kapatabilirsiniz.
Geçerli olan kelimeler:

- `close`
- `closes`
- `closed`
- `fix`
- `fixes`
- `fixed`
- `resolve`
- `resolves`
- `resolved`

Eğer aynı mesaj içinde birden fazla issue kapatmak isterseniz;

```bash
git commit -m 'Fixes #11, fixes #12, resolves #22
```

gibi yazabilirsiniz.

Kaynak: https://help.github.com/en/articles/closing-issues-using-keywords

## Kısa Yollar

Kendi `~/.gitconfig`’iniz için kullanışlı bazı kısa yollar;

```ini
[alias]
    add-modified            = !"git status -sb | grep '^ M ' | sed 's/ M //' | xargs git add"
    add-untracked           = !"git status -sb | grep '^??' | sed 's/?? //' | sed 's/.*/\"&\"/' | xargs git add"
    branches                = for-each-ref --sort=-committerdate --format='%(color:white)[%(refname:short):%(color:yellow)%(objectname:short)%(color:reset)]%(color:reset) \t %(color:red)%(authorname)%(color:reset) \t %(color:blue)%(authordate:relative)%(color:reset)' refs/remotes
    show-ignored            = "ls-files -o -i --exclude-standard"
    show-silent-ignores     = !"git ls-files -v | grep ^h"
    remove-from-index       = "update-index --assume-unchanged"
    put-backto-index        = "update-index --no-assume-unchanged"
    show-todays-diff        = "diff --shortstat '@{0 day ago}'"
    show-untracked          = "ls-files --others --exclude-standard"
    add-deleted             = "add -u"
    root-commit             = "rev-list --max-parents=0 HEAD"
    next-commit             = !"git checkout $(git log --reverse --ancestry-path --pretty=%H HEAD..master | head -1)"
    unstage                 = "reset HEAD --"
    uncommit                = "reset --soft HEAD^"
    wip                     = !"git add -A; git ls-files --deleted -z | xargs -0 git rm; git commit -m 'wip'"
    initial-commit-tr       = "commit --allow-empty -m'[root] İlk commit'"
    initial-commit          = "commit --allow-empty -m'[root] Initial commit'"
    set-bilgi-commit-temp   = "config commit.template ~/.gitcommittemplatebilgi"
    br                      = "branch -v"
    bra                     = "branch -avv"
    brd                     = "branch -d"
    brm                     = "branch --no-mergedd"
    brnm                    = "branch --no-merged"
    brr                     = "branch -rv"
    ci                      = "commit"
    cia                     = "commit --amend -C HEAD"
    ki                      = "kommit"
    co                      = "checkout"
    df                      = "diff --word-diff"
    dfn                     = "diff --name-only"
    fc                      = "commit --allow-empty -m"
    lg                      = "log --graph --decorate --oneline --all"
    lg2                     = "log --graph --decorate --oneline --all --date=relative --pretty='%C(auto)%h%C(reset) -%C(auto)%d %C(reset)%C(white)%s%C(reset)%n%>(14)%C(dim white) %an (%aE %G?), %ad%C(reset)%n'"
    list-remote-tags        = "ls-remote --tags"
    ls                      = "ls-files"
    pullr                   = "pull --rebase"
    rmt                     = "remote -v"
    st                      = "status"
    stu                     = "status --untracked-files"
    sti                     = "status --ignored"
    sts                     = "status -sb"
    track-origin-master     = "branch --set-upstream-to=origin/master master"

[color]
    ui = auto

[color "branch"]
    current = yellow reverse
    local = yellow
    remote = green

[color "diff"]
    meta = yellow bold
    frag = magenta bold
    old = red bold
    new = green bold

[color "status"]
    added = yellow bold
    changed = green
    untracked = red

[core]
    excludesfile = ~/.gitignore
    whitespace = fix,-indent-with-non-tab,trailing-space,cr-at-eol
    pager = less -FRX
    autocrlf = input
    safecrlf = true
    disambiguate = commit
    abbrev = 12

[push]
    default = tracking

[fetch]
    prune = true

[rerere]
    enabled = true

[help]
    autocorrect = 0

[diff]
    compactionHeuristic = 1

[status]
    submoduleSummary = true

[showbranch]
    default = --topo-order
    default = heads/*

[blame]
	coloring = highlightRecent

[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
```

Global `~/.gitignore` örneği:

```bash
# OS Files
##############################################################################
.DS_Store
#Icon?
# Thumbnails
._*

# Files that might appear on external disk
.Spotlight-V100
.Trashes
##############################################################################
__pycache__/
*.py[cod]
xcuserdata
.sass-cache
.bundle
vendor/bundle
*.swp
.tm_properties
.vscode
```

Eğer proje bazlı farklı `.gitignore` ihtiyaçları için [gitignore.io][gitignore]’yu 
kullana bilirsiniz.

## Yardımcı Araçlar

- [homebrew][homebrew]
- [tig][tig] (`brew install tig`)
- [git-extras][git-extras] (`brew install git-extras`)

## Linkler

- http://git.gelistiriciyiz.biz


[homebrew]:              https://brew.sh
[tig]:                   https://github.com/jonas/tig
[git-extras]:            https://github.com/tj/git-extras
[gitignore]:             https://gitignore.io

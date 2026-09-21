# cicd-workflows

Gedeelde CI/CD-templates en composite actions voor .NET-solutions met
matrix-builds en GitOps-promotie (devtest → acceptatie → productie).

## Gebruik (consumer-contract)

Elke consumer-repo levert:
- Dunne callers `.github/workflows/ci.yml` / `cd.yml` (triggers, permissions,
  `with`, `secrets: inherit`; géén top-level `concurrency` op callers).
- `.github/projects.json` met per project `image_name`, `chart`, `paths`,
  `test-paths` plus `_global` (`select-all`, `ignore`).
- Code onder `src/`, tests onder `tests/`, charts onder `.infra/`.
- GitHub Environments `acceptatie` + `productie` met required reviewers.
- Vars `GITEA_USER` (+ registry-host) en secrets `GITEA_TOKEN`, `GH_PAT`.
- .NET-config: `global.json`, `version.json` per project (unieke major.minor).

Callers pinnen een tag, bv.:
`uses: bergconnect/cicd-workflows/.github/workflows/cd-template.yml@v1`.

## Releasen

1. Wijzig via PR tegen `main` (PR-only, geen directe pushes).
2. Valideer lokaal: `yamllint` (alleen bekende warnings) + awk ≤80.
3. Integratiebewijs = groene LearnCICD CI/CD-runs op de consumer-PR.
4. Tag `vMAJOR.MINOR` (major bij breaking inputs/outputs, anders minor) en
   GitHub Release met dezelfde naam. Templates + actions releasen ALTIJD
   samen; action-refs in templates wijzen naar dezelfde tag.

## Bekende lint-uitzondering

Twee `uses:`-regels (centrale action-refs) zijn 83 tekens en kunnen niet
vouwen zonder YAML-semantiek te breken — geaccepteerd, niet fixen.

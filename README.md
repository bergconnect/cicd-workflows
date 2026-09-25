# cicd-workflows

Gedeelde CI/CD-templates en composite actions voor .NET-solutions met
matrix-builds en GitOps-promotie (dev → prd).

## Gebruik (consumer-contract)

Elke consumer-repo levert:
- Dunne callers `.github/workflows/ci.yml` / `cd.yml` (triggers, permissions,
  `with`, `secrets: inherit`; géén top-level `concurrency` op callers).
- `.github/projects.json` met per project `image_name`, `chart`, `paths`,
  `test-paths` plus `_global` (`select-all`, `ignore`).
- Code onder `src/`, tests onder `tests/`, charts onder `.infra/`.
- GitHub Environment `prd` met required reviewers (dev heeft geen gate).
- Vars `GITEA_USER` (+ registry-host) en secrets `GITEA_TOKEN`, `GH_PAT`.
- .NET-config: `global.json`, `version.json` per project (unieke major.minor); `global.json` is tevens de enige SDK-versiebron.

Callers pinnen een tag, bv.:
`uses: bergconnect/cicd-workflows/.github/workflows/promote-template.yml@v11`.

## Releasen

1. Wijzig via PR tegen `main` (PR-only, geen directe pushes).
2. Valideer lokaal: `yamllint` (alleen bekende warnings) + awk ≤80.
3. Integratiebewijs = groene LearnCICD CI/CD-runs op de consumer-PR.
4. Tag `vMAJOR.MINOR` (major bij breaking inputs/outputs, anders minor) en
   GitHub Release met dezelfde naam. Templates + actions releasen ALTIJD
    samen; action-refs in templates wijzen naar dezelfde tag. Verifieer dat
 laatste expliciet (drie keer gemist vóór v4.1 — onderstaand commando
 met de nieuwe tag ingevuld moet leeg zijn):
 ```bash
 grep -rn "cicd-workflows/.github/actions/" .github/workflows/ | grep -v "@<nieuwe-tag>"
 ```

## Bekende lint-uitzondering

Twee `uses:`-regels (centrale action-refs) zijn 83 tekens en kunnen niet
vouwen zonder YAML-semantiek te breken — geaccepteerd, niet fixen.

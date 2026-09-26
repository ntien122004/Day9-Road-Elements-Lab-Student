# EDGE CASE CARDS — INTERNAL OWNER REFERENCE

**Version:** v2

Internal gold-owner material. Do not include these cards in the peer blind pack. `Sample` stays `chưa gán` until the owner selects an actual image and reviews it at full resolution. The active sample pack uses still images from GTSDB and BDD100K; LISA is out of scope.

Each card describes a case that could produce reasonable annotation differences. Expected decisions follow the active v2 guideline and should be confirmed against the actual image before gold is frozen.

---

CASE ID: EC-001
Sample: chưa gán
Scene: Sign partly covered by foliage
Observation: Leaves or branches hide part of a sign face.
Decision: LABEL if the face is confirmed.
Expected: One `traffic_sign` box around the visible face only. Set `visibility=partial`; use `identification=unknown` and `sign_code=unknown` when details cannot be identified.
Rationale: Do not estimate hidden geometry.
Common mistake: Expanding the box to an imagined full sign.
Diversity: occlusion

---

CASE ID: EC-002
Sample: chưa gán
Scene: Sign partly covered by a vehicle
Observation: A vehicle overlaps part of the sign.
Decision: LABEL if the sign is confirmed.
Expected: Box only the visible sign face, excluding the vehicle. Set `visibility=partial`.
Rationale: The occluder is not part of the sign geometry.
Common mistake: Including the vehicle or support in the box.
Diversity: occlusion

---

CASE ID: EC-003
Sample: chưa gán
Scene: Small distant sign-like object
Observation: A small object in the background may be a sign or another road object.
Decision: LABEL if confirmed; IGNORE if evidence is insufficient; use image-level UNCERTAIN if the image contains an unresolved candidate.
Expected: If confirmed, draw a box on the visible face and set `visibility=low`; unknown details use the relevant `unknown` attribute. Do not create a speculative box.
Rationale: Avoid both missed signs and false positives.
Common mistake: Inferring a sign from a circle or triangle alone.
Diversity: small_far

---

CASE ID: EC-004
Sample: chưa gán
Scene: Advertisement that resembles a traffic sign
Observation: An advertisement uses a familiar shape or color.
Decision: IGNORE.
Expected: No `traffic_sign` box; use `image_status.status=contains_sign` only if another in-scope sign is present, otherwise `no_sign` after checking the full image.
Rationale: Appearance alone does not establish traffic-sign function.
Common mistake: Labeling by color/shape without traffic context or sign evidence.
Diversity: negative

---

CASE ID: EC-005
Sample: chưa gán
Scene: Several sign faces on one pole
Observation: Multiple physical faces are mounted together.
Decision: LABEL each face separately.
Expected: One `traffic_sign` instance per distinct visible face; do not merge the group or include the pole.
Rationale: Each face is independently detected and classified.
Common mistake: One box around the whole assembly.
Diversity: conflict

---

CASE ID: EC-006
Sample: chưa gán
Scene: Supplementary plate below a main sign
Observation: A separate plate adds a condition or restriction.
Decision: LABEL each distinct face separately when it is a traffic sign directed to road users.
Expected: A separate instance with `sign_family=supplementary` when supported; otherwise use `unknown` rather than merging it into the main sign.
Rationale: The ontology represents family as an attribute, not a combined object.
Common mistake: Merging two faces or guessing the plate's code.
Diversity: ambiguity

---

CASE ID: EC-007
Sample: chưa gán
Scene: Sign almost completely occluded
Observation: Only a small portion remains visible.
Decision: LABEL only if the visible evidence confirms a sign; otherwise do not create a speculative box and escalate the image-level uncertainty.
Expected: For a confirmed sign use a visible-only box, `visibility=severely_occluded`, and unknown identification/code as needed.
Rationale: A real sign should not be dropped merely because its code is unreadable, but uncertain objects must not become false positives.
Common mistake: Guessing the code or labeling an unconfirmed fragment.
Diversity: critical

---

CASE ID: EC-008
Sample: chưa gán
Scene: Sign cut by the image boundary
Observation: Part of the face lies outside the frame.
Decision: LABEL the visible portion if it is confirmed.
Expected: Box stays inside the image and follows the visible face; set `visibility=partial`.
Rationale: Annotation is visible geometry, not amodal reconstruction.
Common mistake: Extending the box outside the image.
Diversity: occlusion

---

CASE ID: EC-009
Sample: chưa gán
Scene: Sign viewed at a strong angle
Observation: Perspective makes the face look skewed.
Decision: LABEL.
Expected: Rectangle follows the visible face in the image; do not mentally fronto-parallel rectify it.
Rationale: Geometry must match the pixels.
Common mistake: Enlarging or reshaping the box to represent an imagined frontal sign.
Diversity: geometry

---

CASE ID: EC-010
Sample: chưa gán
Scene: Confirmed sign with unreadable family or code
Observation: The object is clearly a road-user sign, but its details cannot be read.
Decision: LABEL and use UNKNOWN for only the unsupported fields.
Expected: Keep the object box. Set `sign_code=unknown` when code is unreadable; set `sign_family=unknown` and `identification=unknown` only when those are also unsupported.
Rationale: Detection and classification are separate decisions.
Common mistake: Dropping a confirmed sign because the code is unknown.
Diversity: ambiguity

---

CASE ID: EC-011
Sample: chưa gán
Scene: Repeated sign across video frames
Observation: The current pack contains still images only.
Decision: Not applicable to this task.
Expected: Annotate each supplied image independently; do not create tracks or copy attributes across images.
Rationale: The task has no temporal identity contract.
Common mistake: Treating similar signs in different images as one track.
Diversity: temporal

---

CASE ID: EC-012
Sample: chưa gán
Scene: Sign from a system without a known GTSDB code
Observation: A sign is relevant to road users but no matching code exists in the project's GTSDB-based code reference.
Decision: LABEL if the sign is in scope; do not fabricate a German code.
Expected: Assign a supported family if possible; enter `sign_code=unknown` when no code mapping is supported; escalate if scope/family cannot be resolved.
Rationale: The project targets driving-relevant signs and includes BDD100K scenes; absence of a GTSDB code is not evidence to ignore a real sign.
Common mistake: Inventing or translating a code to fit the GTSDB scheme.
Diversity: conflict

---

CASE ID: EC-013
Sample: chưa gán
Scene: Annotators propose different sign codes
Observation: The sign is confirmed but code interpretations differ.
Decision: Use `sign_code=unknown` if the visible evidence does not resolve the code; escalate if an owner decision is needed.
Expected: Do not decide by majority or confidence alone. Record the evidence and update the guide if the ambiguity reveals a missing rule.
Rationale: Disagreement is evidence to diagnose, not proof that an annotator is wrong.
Common mistake: Copying the most confident annotator's code without evidence.
Diversity: conflict

---

CASE ID: EC-014
Sample: chưa gán
Scene: Motion blur or low contrast
Observation: The face is blurred but may still be identifiable as a sign.
Decision: LABEL if confirmed and boxable.
Expected: Use `visibility=low`; use unknown identification/code where details are not supported. If sign presence itself is uncertain, use `image_status.status=uncertain` and do not draw a speculative box.
Rationale: Reduced legibility does not automatically mean the object is out of scope.
Common mistake: Guessing from context or dropping a confirmed sign.
Diversity: low_visibility

---

CASE ID: EC-015
Sample: chưa gán
Scene: Cannot decide whether the candidate is an in-scope sign
Observation: Available pixels do not establish sign identity or road-user function.
Decision: ESCALATE at image level if a decision is needed.
Expected: Do not create a speculative box. Add the `image_status` tag with `status=uncertain`; record the question and evidence for review.
Rationale: The schema has no image-level review attribute; `image_status.status=uncertain` is the visible export decision.
Common mistake: Setting `review_status=escalate` without an object, or silently treating the image as `no_sign`.
Diversity: escalation
